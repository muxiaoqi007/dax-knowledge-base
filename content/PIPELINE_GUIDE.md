# Grid Pipeline 完整技术手册

> 基于 2026-08 代码质检与流程梳理结果编写。所有结论均已与源码交叉核对，文件路径和行号可直接点击跳转。

---

## 目录

1. [项目概述](#1-项目概述)
2. [端到端数据流程](#2-端到端数据流程)
3. [DuckDB 数据血缘](#3-duckdb-数据血缘)
4. [首次运行步骤](#4-首次运行步骤)
5. [后续月度增量步骤](#5-后续月度增量步骤)
6. [某分组新增分子的步骤](#6-某分组新增分子的步骤)
7. [代码质检 Findings](#7-代码质检-findings)
8. [文档与代码差异清单](#8-文档与代码差异清单)
9. [验证 SQL 速查](#9-验证-sql-速查)

---

## 1. 项目概述

本项目是一套两阶段 LightGBM 药店销售预测系统，覆盖全国 66 万+ 药店，按分子分组（580 个 ATC 分组）预测月度销售潜力，并将预测结果从网格粒度拆分到 `城市 × 省份 × drug_code` 粒度，最终汇入统一交付表 `drug_retail_scaling`。

**运行环境**

- 生产 VPS 路径：`/home/cicd/Grid`
- Python 环境：`~/venv/bin/python`
- LightGBM 版本兼容路径：`PYTHONPATH=/home/cicd/Grid/.lgb4`（LightGBM 3.3.5 与 NumPy 2.2.6 存在不兼容，需单独 venv）
- DuckDB 主库：`data_analysis.duckdb`（约 38 GB，不入 Git）
- 模型文件：`models/`（不入 Git）
- StarRocks 连接：按源 IP 限制，VPS 不一定能直连，缓存步骤需在允许 IP 的机器执行

**依赖包**（仓库无 requirements.txt，需手动安装）

```
duckdb lightgbm scikit-learn pandas numpy joblib PyMySQL SQLAlchemy pyyaml requests
```

---

## 2. 端到端数据流程

### 2.1 整体架构

```
StarRocks (pcube 库)
  ├─ dim_mf_grid_features_f
  ├─ view_yymf_busi_grid_sales_agg
  ├─ view_yymf_busi_grid_pharmacy_agg
  ├─ dim_drug_to_mf
  └─ med_alpha_dp_depart_drug_sale_new
          │
          │ cache_to_duckdb.py
          │ load_raw_features.py
          │ cache_all_molecule_sales.py
          ▼
DuckDB (data_analysis.duckdb)
  ├─ raw_pharmacy_features     ← 药店主数据 CSV
  ├─ grid_sales_pharmacy       ← 面板覆盖
  ├─ grid_molecule_sales       ← 网格×分子×月销售
  ├─ grid_drug_sales           ← 网格×drug_code×月销售
  └─ molecule_mapping          ← 分子-分组映射
          │
          │ P1: predict_pharmacy_potential.py
          ▼
  pharmacy_potential           ← 药店×分子月度潜力权重
          │
          │ P2: scale_to_province.py
          ▼
  {group}_city_potential       ← 城市×月×inn×drug_code 金额
          │
          │ build_drug_retail_scaling.py
          ▼
  drug_retail_scaling          ← 统一交付表
          │
          │ build_webapp_preagg.py 等
          ▼
  drs_* / qc_* / pp_* 预聚合表
```

### 2.2 "放大"的含义

放大不是乘固定倍数。P2 构造三个量（`scale_to_province.py:955-1155`）：

- `a` = 样本月实际销售（面板覆盖药店的真实金额）
- `b` = 模型预测：`分类概率 × expm1(回归预测) × Duan smearing`（smearing 截断到 0.5-3.0）
- `c` = HT/贝叶斯基线：`total_pharmacies × (a + 30μ) / (coverage + 30)`

最终网格总销售 `C = max(a, w·b + (1-w)·c)`，其中 `w = CV R²`。

非样本新增量 `C - a` 按网格/省份当月 drug_code 结构混合权重（`(p1·w1 + p2·w2/4)/(w1+w2/4)`）拆到 drug_code，再按 P1 药店潜力占比分配到城市。

**固定组倍数（2×-40×）只用于 fallback**：无法建模或当月新出现分子的情况。

### 2.3 P1 药店潜力详细流程

入口：`scripts/predict_pharmacy_potential.py:639`，生产参数 `--use-unified-schema --no-csv`

1. 从 `raw_pharmacy_features` 聚合网格特征 → `grid_features_unified`（30 列）
2. 取 2024 年历史特征：`mol_avg_sales_2024`、`mol_avg_selling_2024`、`mol_months_2024`
3. 取 2025 年数据训练 LightGBM 分类 + 回归（5 折 CV，`random_state=42`）
   - 分类：有该分子销售的网格为正样本，有面板覆盖但无销售为负样本
   - 回归目标：`geo_mean_pharmacy_sales × total_pharmacies × selling/coverage`，需 `selling >= 2`
4. 网格预测 = `分类概率 × expm1(回归预测)`
5. 药店分配权重优先级：DTP lift > pooled affinity > `log1p(2024收入)`
6. 过滤低于 `0.1 × 分子单位售价` 的药店，最多迭代 3 次

输出：`pharmacy_potential(group_name, esid, alpha_num_field, inn_name, monthly_potential, weight)`

### 2.4 P2 网格放大详细流程

入口：`scripts/scale_to_province.py:1582`，默认 `estimator=ratio`，`shrinkage_tau=30`

1. 同样用 2025 年训练分类 + 回归（`selling >= 2`，行数 >= 500 时收紧到 `selling >= 3`）
2. 计算 Duan smearing（截断 0.5-3.0），CV R² 裁为 0 下限
3. 月度 apply：构造 a/b/c，融合得 C
4. drug_code 拆分（inner join，见 [P1-3 质检](#p1-3)）
5. 城市拆分：从 P1 `pharmacy_potential` 取各城市药店潜力占比

输出：`{group}_city_potential(城市, 省份, compute_month, inn_name, drug_code, 金额, 数量, 价格, 金额_s, 金额_ns)`

---

## 3. DuckDB 数据血缘

**主库**：`data_analysis.duckdb`，默认 `main` schema，无持久 VIEW，无 MERGE INTO。

### 3.1 基础与销售表

| 表 | 关键列 | 上游来源 | 创建脚本 | 更新语义 |
|---|---|---|---|---|
| `grid_features` | 继承 StarRocks，完整列不可静态确定 | StarRocks `dim_mf_grid_features_f` | `cache_to_duckdb.py:62-78` | DROP+CTAS 全量 |
| `grid_sales_pharmacy` | `pharmacy_label, inn_name, sales, volume, pharmacy_count_base, grid_pharmacy_count` | StarRocks `med_alpha_dp_depart_drug_sale_new` JOIN `dim_drug_to_mf` JOIN `med_alpha_dp_virtual_depart`（按月分批拉取，DuckDB 本地聚合） | `cache_grid_sales_pharmacy.py` | 全量替换；时间窗口为滚动最近 12 个月，`grid_pharmacy_count` 与销售窗口对齐 |
| `drug_mapping` | `drug_code, inn_name` | StarRocks `dim_drug_to_mf` | `cache_to_duckdb.py:43-46` | 全量替换（已被 `molecule_mapping` 取代） |
| `raw_pharmacy_features` | `esid, alpha_num_field, lon, lat, type, 连锁门店数, 2024年总销售金额, population_in_1km/3km, avg_income, total_households, 三级/二级医院_in_300m_num, total_beds, total_visits, 商圈/小区_in_300m_num, 药店_in_3km, DTP/医保/双通道/院边店/社区标签, 省` | `data/药店主数据建模用特征.csv` | `load_raw_features.py:11-20` | CREATE OR REPLACE，CSV 快照 |
| `grid_features_unified` | 30 列聚合网格特征（见 `predict_pharmacy_potential.py:173-205`） | `raw_pharmacy_features` | P1/P2 训练时重建 | 每次训练全量重建 |
| `grid_molecule_sales_staging` | `grid_id, inn_name, compute_month, selling_pharmacies, geo_mean_pharmacy_sales, total_sales, total_volume` | StarRocks `med_alpha_dp_depart_drug_sale_new` JOIN `dim_drug_to_mf` | `cache_all_molecule_sales.py:47-55,72-100` | 按缺失月 append；`--force` 清空重拉 |
| `grid_drug_sales_staging` | `grid_id, inn_name, drug_code, compute_month, total_sales, total_volume` | 同一 StarRocks 销售表 | `cache_all_molecule_sales.py:56-61,103-120` | 按月 append（**完成判断仅看 GMS staging，有不一致风险**） |
| `grid_molecule_sales` | `group_name, grid_id, inn_name, compute_month, selling_pharmacies, geo_mean_pharmacy_sales, total_sales, total_volume` | staging LEFT JOIN `inn_group_mapping` | `cache_all_molecule_sales.py:213-227` | CREATE OR REPLACE |
| `grid_drug_sales` | `group_name, grid_id, inn_name, drug_code, compute_month, total_sales, total_volume` | staging + group mapping | `cache_all_molecule_sales.py:243-256` | 全量替换 |
| `molecule_mapping` | `group_name, drug_code, inn_name` | mapping staging + `inn_group_mapping` | `cache_all_molecule_sales.py:231-239` | 全量替换 |

### 3.2 模型注册表

| 表 | 关键列 | 写入脚本 | 更新语义 |
|---|---|---|---|
| `training_runs` | `run_id, pipeline, group_name, started_at, finished_at, duration_seconds, status, command_line, n_molecules, n_molecules_ok, n_molecules_skip, estimator, use_unified_schema, notes` | `model_persistence.py:54-72,137-165` | 按 run 追加 |
| `model_training_log` | 23 列，含 `run_id, inn_name, stage, cv_r2, smearing, n_train_rows, skipped_reason` | `model_persistence.py:73-98,168-199` | INSERT OR REPLACE，键 `(run_id, inn_name, stage)` |
| `model_registry` | `run_id, pipeline, group_name, inn_name, stage, version, model_path, created_at, ...` | `model_persistence.py:99-114,202-247` | 每版本 INSERT，不覆盖旧版 |
| `model_feature_importance` | `run_id, inn_name, stage, feature_name, importance, rank` | `model_persistence.py:115-125,265-283` | 每 run/stage/feature upsert |

### 3.3 潜力与放大输出表

| 表 | 关键列 | 上游 | 写入脚本 | 更新语义 |
|---|---|---|---|---|
| `pharmacy_potential` | `group_name, esid, alpha_num_field, inn_name, monthly_potential, weight` | P1 模型 + `raw_pharmacy_features` + affinity | `predict_pharmacy_potential.py:1023-1093` | 按 group DELETE + INSERT |
| `{group}_city_potential` | `城市, 省份, compute_month, inn_name, drug_code, 金额, 数量, 价格, 金额_s, 金额_ns` | P2 模型 + `grid_molecule_sales` + `grid_drug_sales` + `pharmacy_potential` + `data/药店主数据分组.csv` | `scale_to_province.py:1375-1388` | 全量 DROP+CTAS；月更 DELETE month + INSERT |
| `drug_retail_scaling` | 同上加 `group_name` | 所有 `*_city_potential` | `build_drug_retail_scaling.py:54-68,80-100` | 首批 CREATE OR REPLACE + 后续批次 INSERT |

### 3.4 预聚合与 QC 表（均全量重建，上游为 `drug_retail_scaling`）

| 脚本 | 产出表 |
|---|---|
| `build_webapp_preagg.py:54-151` | `drs_group_month_agg`, `drs_mol_month_agg`, `drs_city_group_month_agg`, `drs_city_mol_month_agg`, `drs_dim_molecules/cities/groups`, `drs_kpi_global` |
| `build_year_preagg.py:39-92` | `drs_year_agg`, `drs_group_year_agg`, `drs_mol_year_agg`, `drs_quarter_agg` |
| `build_insights_preagg.py:41-175` | `drs_drug_labels`, `drs_mol_enriched`, `drs_label_year_agg`, `drs_company_year_agg`（上游含外部导入表 `dim_drug_enriched`） |
| `build_pharmacy_preagg.py:49-167` | `pp_dim_pharmacy`, `pp_mol_prov_agg`, `pp_mol_type_agg`, `pp_mol_summary`, `pp_dim_groups` |
| `build_qc_preagg.py:40-144` | `qc_mol_month`, `qc_drug_month`, `qc_group_month`, `qc_new_products` |
| `build_qc_scaling_preagg.py:53-182` | `qc_scaling_model`, `qc_scaling_sample_coverage`, `qc_scaling_mol_month` |
| `build_qc_blend_diagnostics.py:41-65` | `qc_scaling_blend_quantiles`（按 group DELETE+INSERT，可 `--resume`） |

### 3.5 仓库外部依赖

以下表仅有消费端代码，建表脚本不在仓库：

- `inn_group_mapping`：可从生产快照恢复，或从 `config/atc/` YAML 本地构建（见第 4.4 节）。新增分子/分组后需运行 `scripts/rebuild_main_tables.py` 重建 `grid_molecule_sales`、`grid_drug_sales`、`molecule_mapping` 三张主表，让 `group_name` 重新回填。字段存在冲突：缓存脚本读 `group_key`（`cache_all_molecule_sales.py:217`），运维文档称 `final_group_key`（`OPERATIONS_RUNBOOK.md:93-98`）。
- `dim_drug_enriched`：仅消费端（`build_insights_preagg.py:41-73`）
- `sample_pharmacy_real_sales`、`esid_pharmacy_id_virtual_bridge`、`pharmacy_type_bridge`：仅消费端（`predict_pharmacy_potential.py:556-788`）
- `pp_dim_pharmacy_names`：由 `master_pharmacy_202603.parquet` 内存构建后导出，主库导入代码缺失

---

## 4. 首次运行步骤

> **前提**：`data_analysis.duckdb` 不入库，必须从 StarRocks 重建或从生产快照恢复。`inn_group_mapping` 建表脚本不在仓库，须从生产快照恢复。

### 4.1 环境准备

```bash
cd /home/cicd/Grid
export PYTHONPATH=/home/cicd/Grid/.lgb4
PY=$HOME/venv/bin/python

# 安装依赖（仓库无 requirements.txt，手动安装）
pip install duckdb lightgbm scikit-learn pandas numpy joblib PyMySQL SQLAlchemy pyyaml requests

# 凭据
cp .env.example .env
# 编辑 .env，至少填入 GRID_SR_PASSWORD
```

> **注意**：StarRocks 按源 IP 白名单，VPS 不一定能直连。数据缓存步骤（4.2、4.4）需在允许 IP 的机器执行。

### 4.2 药店主数据（依赖外部 CSV）

```bash
# 前提：data/药店主数据建模用特征.csv 已就位
$PY scripts/load_raw_features.py
# 建表：raw_pharmacy_features, grid_features_enhanced
```

### 4.3 静态网格数据

```bash
# 步骤 1：缓存网格特征和 drug_mapping（cache_to_duckdb.py 不再负责 grid_sales_pharmacy）
$PY scripts/cache_to_duckdb.py
# 建表：grid_features, drug_mapping
# 警告：脚本以 os._exit(0) 结束，异常会被吞掉，需检查表是否实际建成

# 步骤 2：按月分批构建 grid_sales_pharmacy（绕开 StarRocks 聚合视图 OOM）
$PY scripts/cache_grid_sales_pharmacy.py
# 建表：grid_sales_pharmacy
# 自动查 MAX(compute_month) 后向前滚动 12 个月；sales 和 grid_pharmacy_count 使用同一窗口
# 注意：原 view_yymf_busi_grid_sales_agg 和 view_yymf_busi_grid_pharmacy_agg
#       在服务器内存紧张时（进程占用 20GB+）会触发 4GB per-query OOM，
#       本脚本改为逐月查底层事实表后 DuckDB 本地聚合，完全绕开服务端限制。
```

### 4.4 准备 inn_group_mapping

`cache_all_molecule_sales.py` 要求该表已存在，否则直接退出（`cache_all_molecule_sales.py:205-210`）。

**方案 A（推荐）**：从生产 DuckDB 快照恢复——保留完整历史分组配置。

**方案 B**：本地从 YAML 构建最小版本（仅包含当前 `config/atc/` 下各组分子）：

```python
import duckdb, yaml, os, glob

con = duckdb.connect('data_analysis.duckdb')
con.execute('''CREATE TABLE IF NOT EXISTS inn_group_mapping (
    inn_name VARCHAR, group_key VARCHAR, final_group_key VARCHAR
)''')
for path in glob.glob('config/atc/*.yaml'):
    cfg = yaml.safe_load(open(path))
    group = cfg['group_name']
    for mol in cfg.get('molecules', []):
        con.execute('INSERT INTO inn_group_mapping VALUES (?, ?, ?)',
                    [mol, group, group])
print(con.execute('SELECT COUNT(*) FROM inn_group_mapping').fetchone())
con.close()
```

新增分子或分组变更后，需运行 `scripts/rebuild_main_tables.py` 重建主表（见第 6 节）。

### 4.5 全量缓存销售数据（耗时较长）

```bash
$PY scripts/cache_all_molecule_sales.py
# 默认拉 202101-202602，建：grid_molecule_sales, grid_drug_sales, molecule_mapping
# ⚠️ 不要用 --force --only-months 组合，会清空所有历史（见质检 P1-6）
```

缓存完成后必须分别验证 GMS 和 GDS 均有数据，不能只看脚本退出码：

```sql
SELECT COUNT(*), MIN(compute_month), MAX(compute_month) FROM grid_molecule_sales_staging;
SELECT COUNT(*), MIN(compute_month), MAX(compute_month) FROM grid_drug_sales_staging;
```

### 4.6 初始化模型元数据表

```bash
$PY scripts/model_registry_init.py
# 建表：training_runs, model_training_log, model_registry, model_feature_importance
```

### 4.7 运行前检查

```bash
$PY scripts/preflight_check.py
# 检查磁盘（代码阈值 60GB）、必需表、StarRocks、Python 包、models/ 可写
```

### 4.8 全量 P1 + P2（约数小时）

```bash
$PY scripts/run_full_rollout.py \
  --pipeline p1p2 \
  --n-jobs 4 \
  --sort-by size_asc
# 断点续跑：重复运行，已完成组通过 output/full_rollout_done.txt 跳过
# ⚠️ wave_chain.py 不等价，它的 P2 只做 GMV Top30（wave_chain.py:42-51）
```

### 4.9 统一表与预聚合

```bash
$PY scripts/build_drug_retail_scaling.py
$PY scripts/build_webapp_preagg.py
$PY scripts/build_qc_scaling_preagg.py
```

---

## 5. 后续月度增量步骤

月更入口**不拉取 StarRocks 数据**，假定目标月已在 `grid_molecule_sales`/`grid_drug_sales` 中。必须先单独缓存。

### 5.1 第一步：缓存新月数据（在允许 IP 的机器）

```bash
$PY scripts/cache_all_molecule_sales.py --only-months YYYYMM

# 缓存后分别验证 GMS 和 GDS 都有目标月
python -c "
import duckdb
con = duckdb.connect('data_analysis.duckdb', read_only=True)
for t in ['grid_molecule_sales_staging', 'grid_drug_sales_staging']:
    r = con.execute(f'SELECT COUNT(*), MIN(compute_month), MAX(compute_month) FROM {t}').fetchone()
    print(t, r)
"
```

### 5.2 第二步：执行月更（在 VPS）

```bash
cd /home/cicd/Grid
export PYTHONPATH=/home/cicd/Grid/.lgb4
bash /home/cicd/Grid/run_monthly_increment.sh YYYYMM
```

月更内部顺序（`run_monthly_increment.sh:33-115`）：
1. 检查目标月是否已在 `grid_molecule_sales`
2. 生成组列表（优先 `scratchpad_import/groups.txt`，否则从 `model_registry` 查；**注意**：fallback SQL 未过滤 pipeline，见质检差异 #8）
3. 停止 `grid-webapp`（释放 DuckDB 写锁）
4. 逐组运行 `predict_incremental_month.py --from-registry --fast`（只 apply，不重训）
5. 运行组系数 fallback（`scale_fallback_group_multiplier.py`）
6. 重建 `drug_retail_scaling`
7. 刷新 webapp/QC 预聚合
8. 生成月度 QC
9. 重启 `grid-webapp`

### 5.3 验证月更结果

```bash
# 查看每组执行状态（DONE ≠ 全组成功）
grep -E '^(OK|FAIL) ' output/increment_YYYYMM_*/summary.log

# QC 报告（exit=2 需人工确认）
cat output/increment_YYYYMM_*/qc_report.txt

# 统一表覆盖验证
python -c "
import duckdb; con=duckdb.connect('data_analysis.duckdb', read_only=True)
print(con.execute('''
  SELECT compute_month,
         COUNT(*) rows,
         COUNT(DISTINCT group_name) groups,
         COUNT(DISTINCT inn_name) molecules,
         SUM(金额) amount
  FROM drug_retail_scaling WHERE compute_month=? GROUP BY 1
''', ['YYYYMM']).fetchdf())
"
```

---

## 6. 某分组新增分子的步骤

### 6.1 当月 fallback（自动，无需手动）

月更 fallback 阶段 `scale_fallback_group_multiplier.py` 会自动发现新分子：比较目标月 `grid_drug_sales` 有销售但 city 表无输出的分子，用组倍数（2×-40×）生成城市行，城市分配用药店主数据众数而非 P1 潜力权重。

**这是兜底方案**，精度低于正式建模。

### 6.2 正式建模（需手动操作）

```bash
# 第 1 步：确认 StarRocks 药品字典有该分子
# SELECT DISTINCT drug_code, inn_name FROM pcube.dim_drug_to_mf WHERE inn_name='<NEW_MOL>'

# 第 2 步：更新配置（config/atc/<GROUP>.yaml，加入 molecules 列表）

# 第 3 步：将新分子写入 inn_group_mapping
python -c "
import duckdb
con = duckdb.connect('data_analysis.duckdb')
con.execute(\"INSERT INTO inn_group_mapping VALUES ('<NEW_MOL>', '<GROUP>', '<GROUP>')\")
con.close()
"

# 第 4 步：重建三张主表，让 group_name 正确回填
# （仅改 YAML + inn_group_mapping 后，staging 里的旧数据 group_name 仍是 UNCATEGORIZED）
$PY scripts/rebuild_main_tables.py

# 第 5 步：重新缓存包含新分子的月份（如已有数据则可跳过）
$PY scripts/cache_all_molecule_sales.py --only-months YYYYMM

# 第 6 步：重跑 P1（重训，生成新分子的药店潜力）
# ⚠️ P1 训练窗口固定为 2025 年；上市早于 202501 的分子才有足够样本
# 2025 年后新上市的分子（如埃诺格鲁肽）P1 会跳过，走 fallback 路径
$PY scripts/predict_pharmacy_potential.py \
  --group <GROUP> --use-unified-schema --n-jobs 1 --no-csv

# 第 7 步：重跑 P2（重训，将新模型写入 registry）
$PY scripts/scale_to_province.py \
  --group <GROUP> --use-unified-schema --estimator ratio --n-jobs 1 --no-csv
# ⚠️ YAML 中的 alpha/selling_threshold 等参数当前未被读取（见质检 P2-15）

# 第 8 步：对新分子有数据的月份补跑 fallback（新品首月尤其重要）
$PY scripts/scale_fallback_group_multiplier.py --month YYYYMM

# 第 9 步：此后月更通过 --from-registry 走正式模型路径
# 如需补历史月（主模型分子）：
$PY scripts/predict_incremental_month.py \
  --group <GROUP> --month YYYYMM --from-registry --fast
# 新品 fallback 分子补历史月：
$PY scripts/scale_fallback_group_multiplier.py --month YYYYMM

# 第 10 步：重建统一表和预聚合
$PY scripts/build_drug_retail_scaling.py
$PY scripts/build_webapp_preagg.py
$PY scripts/build_qc_scaling_preagg.py
```

**关于新品分子（上市时间晚于训练窗口）**

P1 和 P2 的训练窗口固定为 2025 年全年数据。如果新分子在 2025 年没有销售记录（例如 2026 年才上市），P1/P2 会合理跳过它——`⚠️ 无销售数据, 跳过` 或 `⚠️ 2025 无销售, 跳过` 是预期行为，不是 bug。这类分子通过 `scale_fallback_group_multiplier.py` 用组倍数补数，直到积累足够的历史月份后才能纳入下一次全量重训的主模型路径。

---

## 7. 代码质检 Findings

### P1 级：会导致数据静默丢失或不可恢复破坏

**P1-1：失败组仍被发布，月更写 `DONE_ALL`**
`run_monthly_increment.sh:83-116`。逐组失败只写 FAIL 不退出，fallback 失败也不阻断，最后无条件重建统一表并写 DONE_ALL。QC 只检查目标月是否有任意行（`check_month_qc.py:42-48`），不验证预期 group/分子覆盖。部分 group 缺失的不完整月份可能被标为完成并交付。

**P1-3：drug_code 拆分因省份无权重整批丢失** <a name="p1-3"></a>
`scale_to_province.py:1323-1332`。`gp JOIN prov_w` 是 inner join，当月某省某分子没有 `grid_drug_sales` 记录时，整批网格预测金额不进任何城市，直接从统一表消失。旧 pandas 路径有跨月权重 fallback，但当前 SQL 主路径（1719-1721 行）没有。典型触发：GMS 有数据而 GDS 缺月，或新品首月。

**P1-4：增量 DELETE + INSERT 非事务，崩溃后永久清空目标月**
`predict_incremental_month.py:147-152`。先删后插无 BEGIN/COMMIT，空预测结果只 return 不返回非零退出码（`predict_incremental_month.py:117`）。

**P1-5：统一表分批重建非原子**
`build_drug_retail_scaling.py:83-94`。第一批 CREATE OR REPLACE 替换 live 表后，后续批次用 INSERT 追加，中间失败留下部分组。

**P1-6：GMS/GDS 缓存可永久固化 GDS 漏数；`--force --only-months` 清空历史**
`cache_all_molecule_sales.py:63-68`。`months_already_done()` 只看 GMS staging，GMS 成功而 GDS 失败后下次运行跳过整月，GDS 永久缺失。`--force --only-months` 会清空全部 staging 再只拉指定月，造成历史数据不可恢复丢失。

**P1-12（安全）：归档代码含明文数据库密码**
`legacy/放大数据参考.py:27-35`。默认值 `DB_UID=linjiyan`，`DB_PWD=Yigetiancai123`，含公网 Azure SQL 连接串。凭据已进入 Git 历史，**应立即轮换**。

### P2 级：影响模型质量或预测准确性

**P1-7：CV 随机切分同网格跨折泄漏，偏乐观 R² 直接改变生产金额**
`scale_to_province.py:412-455`。CV R² 赋给 `mol_r2` 并作为模型与 HT 基线的融合权重（924-925、1173 行），R² 偏高则模型权重偏高，系统性影响最终放大金额。`predict_pharmacy_potential.py:306-335` 同类问题。应改为按 grid 分组或时间切分。

**P1-8：registry 加载混配不同 run/version 的模型和指标**
`scale_to_province.py:707-751`。classifier/regressor 各自独立取最高存在版本，`r2_map`/`smear_map` 从所有历史 training_log 无序覆盖，最终可能是 A run 模型配 B run 指标。

**P1-9：样本真实值锁定误用活跃月均而非完整窗口均**
`pull_sample_store_real_sales.py:44-52`。只保留有销售月份后取 AVG，单月销售的店潜力可能被放大约 6 倍。

**P2-13：QC 不验证完整性，LAG 窗口按行数而非日历月**
`check_month_qc.py:42-48`。漏数可通过 QC；`build_qc_preagg.py:47-69` 用 LAG+ROWS，月份断档时 MoM/YoY 引用错误月份。

### P3 级：配置/文档不一致，不直接影响当前结果

| ID | 问题 | 位置 |
|---|---|---|
| P1-2 | 月度 fallback 跳过无 city 表大组，不报错 | `scale_fallback_group_multiplier.py:216-220` |
| P1-10 | YAML molecule 列表未约束实际训练集，由 mapping 表决定 | `predict_pharmacy_potential.py:117-130` |
| P1-11 | 全量编排失败退出码为 0 | `run_full_rollout.py:161-184`, `wave_chain.py:62-63` |
| P2-14 | `cache_to_duckdb.py` 强制 `os._exit(0)` 吞掉失败 | `cache_to_duckdb.py:93` |
| P2-15 | 580 份 YAML 中的 alpha 等参数全部被生产代码忽略 | `scale_to_province.py:39`, `predict_pharmacy_potential.py:924` |
| P2-16 | 恢复标记无数据/配置指纹，源数据补数后不自动重算 | `run_full_rollout.py:76-85` |
| P2-17 | `migrate_to_long_schema.py` 只扫描 `config/` 根目录，漏掉 `config/atc/` | `migrate_to_long_schema.py:37-45` |
| P2-18 | Stream Load 用明文 HTTP 传 Basic 凭据（当前生产路径未调用） | `sqlengine.py:144-177` |
| P2-19 | 缓存日期范围硬编码到 202602，需显式传 `--only-months` 才拉新月 | `cache_all_molecule_sales.py:34-43` |

---

## 8. 文档与代码差异清单

| # | 文档描述 | 代码实际 | 位置 |
|---|---|---|---|
| 1 | README 示例用 `glp1`/`derma_biologics` | 这两个配置仅在 `config/legacy_20260414/`，`cache_molecule_sales.py` 不扫描 `config/atc/` | `README.md:64-67` |
| 2 | README 声称有宽表输出 `*_potential_wide.csv` | 当前脚本只生成长表，宽表逻辑已移除 | `predict_pharmacy_potential.py:1058-1065` |
| 3 | 运维手册引用 `scripts/run_monthly_refresh.py` | 该文件不存在，当前入口是 `run_monthly_increment.sh` | `OPERATIONS_RUNBOOK.md:144-160` |
| 4 | 运维手册建议 `--molecule-filter` 参数 | `scale_to_province.py` CLI 无此参数 | `scale_to_province.py:1582-1603` |
| 5 | `build_webapp_preagg.py` 注释说产出 `drs_city_month_agg` | 实际产出 `drs_city_group_month_agg`/`drs_city_mol_month_agg` | `build_webapp_preagg.py:5-11` |
| 6 | `build_webapp_preagg.py` 硬编码 Windows 临时目录 | 在 VPS Linux 环境下会直接失败 | `build_webapp_preagg.py:42-48` |
| 7 | CLAUDE.md 写数据库约 940 MB | 运维文档写约 38 GB | `CLAUDE.md:50-53` vs `OPERATIONS_RUNBOOK.md:20-46` |
| 8 | 月更分组列表 fallback SQL 无 pipeline 过滤 | 查询全部 registry 组，可能误入 P1 pipeline 的 group | `run_monthly_increment.sh:47-53` |
| 9 | `preflight_check.py` 注释写"100 GB" | 代码阈值 `MIN_FREE_GB = 60` | `preflight_check.py:2-10,21` |
| 10 | MLflow 列为主要追踪工具 | 生产路径写 DuckDB `training_runs`/`model_registry`，无 MLflow 调用链 | `README.md:186-194` |

---

## 9. 验证 SQL 速查

### 前置表检查

```sql
-- 检查关键表是否存在且有数据
SELECT table_name,
       estimated_size
FROM information_schema.tables
WHERE table_name IN (
  'raw_pharmacy_features','grid_sales_pharmacy',
  'grid_molecule_sales','grid_drug_sales',
  'molecule_mapping','inn_group_mapping','model_registry'
);
```

### 目标月覆盖验证

```sql
-- GMS 目标月
SELECT COUNT(*) rows, COUNT(DISTINCT inn_name) molecules,
       COUNT(DISTINCT grid_id) grids, SUM(total_sales) sample_sales
FROM grid_molecule_sales WHERE CAST(compute_month AS INT) = 202607;

-- GDS 目标月（应与 GMS 行数接近）
SELECT COUNT(*) rows, COUNT(DISTINCT inn_name) molecules,
       COUNT(DISTINCT drug_code) drug_codes, SUM(total_sales) sample_sales
FROM grid_drug_sales WHERE CAST(compute_month AS INT) = 202607;
```

### 新分子链路验证

```sql
SELECT group_name, inn_name,
       COUNT(DISTINCT compute_month) n_months,
       MIN(compute_month) first_month, MAX(compute_month) last_month,
       SUM(total_sales) total_sales
FROM grid_molecule_sales
WHERE inn_name = '<NEW_MOL>'
GROUP BY group_name, inn_name;
```

### 模型完整性检查

```sql
-- 确认 classifier + regressor 都存在
SELECT group_name, inn_name, stage, MAX(version) latest_ver, MAX(created_at) latest_at
FROM model_registry
WHERE pipeline = 'province_scaling' AND group_name = 'XA10BJ'
GROUP BY group_name, inn_name, stage
ORDER BY inn_name, stage;
```

### P1 结果验证

```sql
SELECT group_name,
       COUNT(DISTINCT inn_name) molecules,
       COUNT(DISTINCT esid) pharmacies,
       SUM(monthly_potential) total_potential
FROM pharmacy_potential
WHERE group_name = 'XA10BJ'
GROUP BY group_name;
```

### P2 结果验证

```sql
SELECT compute_month,
       COUNT(*) rows,
       COUNT(DISTINCT inn_name) molecules,
       COUNT(DISTINCT drug_code) drug_codes,
       COUNT(DISTINCT 城市) cities,
       SUM(金额) amount,
       SUM(金额_s) sample_amount,
       SUM(金额_ns) non_sample_amount
FROM XA10BJ_city_potential
GROUP BY compute_month
ORDER BY compute_month;
```

### 统一表验证

```sql
-- 指定月份全分组覆盖检查
SELECT compute_month,
       COUNT(DISTINCT group_name) AS n_groups,
       COUNT(DISTINCT inn_name)   AS n_molecules,
       SUM(金额)                   AS amount
FROM drug_retail_scaling
WHERE compute_month = '202607'
GROUP BY compute_month;

-- 指定分组 2026 年各分子放大汇总（最终交付口径，含主模型和 fallback 分子）
SELECT
    inn_name,
    COUNT(DISTINCT compute_month)     AS n_months,
    COUNT(DISTINCT 城市)               AS n_cities,
    ROUND(SUM(金额) / 1e4, 1)          AS total_wan,
    ROUND(SUM(金额_s) / 1e4, 1)        AS sample_wan,
    ROUND(SUM(金额_ns) / 1e4, 1)       AS nonsample_wan
FROM drug_retail_scaling
WHERE group_name = 'XA10BJ'
  AND CAST(compute_month AS INT) BETWEEN 202601 AND 202612
GROUP BY inn_name
ORDER BY SUM(金额) DESC;

-- 指定分子 Top20 城市（替换 inn_name 和 compute_month）
SELECT 城市, 省份,
       ROUND(SUM(金额) / 1e4, 1)   AS total_wan,
       ROUND(SUM(金额_s) / 1e4, 1) AS sample_wan
FROM drug_retail_scaling
WHERE group_name = 'XA10BJ'
  AND inn_name = '司美格鲁肽'
  AND CAST(compute_month AS INT) = 202607
GROUP BY 城市, 省份
ORDER BY SUM(金额) DESC
LIMIT 20;
```

> **注意**：`drug_retail_scaling` 是最终交付表，包含所有分组、所有分子（主模型路径 + fallback 路径），查放大数据应以此表为准，而不是 `{group}_city_potential`。`pharmacy_potential` 只包含主模型输出的分子，不含 fallback 分子（如新上市分子），不能用它来统计全量放大结果。
