# 药店匹配项目

将外部数据源的药店数据匹配到内部MF主数据（ESID）。通过配置文件管理不同数据源（0701、0702），使用同一套代码完成全流程。

---

## 目录结构

```
药店匹配/
├── config/                         # 配置文件（YAML）
│   ├── config_0701.yaml            # 0701数据源配置
│   └── config_0702.yaml            # 0702数据源配置
│
├── code/                           # 统一流程脚本
│   ├── step00a_master.py           # MF主数据下载+清洗（跨数据源共享，独立运行）
│   ├── step01_download.py          # 外部数据下载（需联网）
│   ├── step02_clean.py             # 外部数据清洗
│   ├── step03_match.py             # 机器匹配 + 未匹配导出 + 全量网格生成
│   ├── step03b_llm_review.py       # LLM辅助审核（替代人工审核non_map文件）
│   └── step04_post_match.py        # 人工审核后处理（融合审核表 + 修正mapping + 重生全量）
│
├── tools/                          # 公共工具函数
│   ├── pharmacy_mapping_main.py    # 清洗/切词/匹配核心函数
│   └── process_data.py             # MF数据下载处理
│
├── shared_data/                    # 跨数据源共享数据
│   ├── df_mf.csv                   # MF主数据
│   ├── df_mf_w_alias.csv           # MF主数据（含别名展开）
│   ├── MF_已清洗.pkl                # MF主数据清洗后（step00a输出）
│   ├── master_ref.csv              # ESID参考表（重定向/软删除）
│   └── download_log.json           # 下载时间记录
│
├── 匹配0701/                       # 0701子项目数据
│   ├── raw/                        # step01下载的原始数据
│   ├── data_clean/                 # step02清洗后的pkl
│   ├── output/                     # step03输出的csv + 执行日志
│   │   └── log_v<版本号>.txt        # 各步骤执行指标自动记录
│   ├── 人工/                       # 人工审核相关文件
│   └── code/                       # 原notebook（归档参考）
│
├── 匹配0702/                       # 0702子项目数据（结构同上）
├── ref/                            # 参考数据（停用词、jieba自定义词典等）
└── archived/                       # 归档文件
```
---
## 使用流程（快速上手）
**以匹配0702为例。如果匹配0701需修改config参数为--config config/config_0701.yaml**

为方便项目交接与快速上手，请按照以下顺序准备配置并依次执行脚本。除标准库初始化外，各核心脚本均通过 `--config` 参数指定对应数据源的配置文件（如 `config/config_0702.yaml`）来实现逻辑复用。

| 步骤/脚本 | 逻辑说明 | 前置条件 | 输出结果 |
| --- | --- | --- | --- |
| **0. 更新配置文件**<br>`config/config_0702.yaml` | 1. 更新 `curr_version`（当期版本）和 `prev_version`（上期版本）。以识别本期全量与增量数据。<br>2. 定义对应数据源的表名、字段映射、匹配阈值等。一般无需修改。| 数据库已更新 药店表.0702药店 | 后续脚本 `step01~04` 将统一读取此配置 |
| **0. 标准库初始化**<br>（需要约20分钟）<br>`python code/step00a_master.py` | 1. 下载药店主数据<br>2. 执行清洗预处理。| 连接到公司内网 | 项目根目录下生成：<br>药店主数据 `shared_data/df_mf.csv`<br>药店主数据展开到异名`shared_data/df_mf_w_alias.csv`<br>药店主数据ESID与ESID变更的对应关系`shared_data/master_ref.csv`<br>药店主数据已清洗预处理`shared_data/MF_已清洗.pkl` |
| **1. 样本相关数据下载**<br>`python code/step01_download.py --config config/config_0702.yaml` | 1. 下载样本相关数据。<br>2. 生成当期样本增量（本期新增 + 上期未匹配），后续仅匹配增量。 | 连接到公司内网<br>已经更新了配置文件config_0702.yaml |样本全量 `匹配0702/raw/0702_full_v20260529_raw`<br>样本增量 `匹配0702/raw/0702_inc_v20260529_process.csv`<br>历史审核结果 `匹配0702/raw/0702_reviewed.csv`<br>上期匹配关系 `匹配0702/raw/0702_v20260427_mapping_prev.csv` <br>执行日志`output/log_v20260529.txt` |
| **2. 样本数据清洗预处理**<br>`python code/step02_clean.py --config config/config_0702.yaml` | 对样本执行相同逻辑的清洗预处理。 | 已运行以上步骤 | 清洗预处理后的样本 `匹配0702/data_clean/0702_v20260529_cleaned.pkl`<br>执行日志追加|
| **3. 机器匹配**<br>`python code/step03_match.py --config config/config_0702.yaml` | 1. 对每个待匹配样本生成搜索范围。 <br>2. 多字段依次精确匹配，模糊匹配，确定搜索范围内最相似的结果。<br>3. 历史审核数据更正部分机器匹配结果。<br>4. 生成机器匹配（置信度高）和未匹配名单（置信度低，生成临时esid以备添加到主数据）。 | 已运行以上步骤 | 增量样本的机器匹配结果 `匹配0702/output/0702_v20260529_mapping.csv`<br>全量样本的机器匹配结果（未匹配的给默认网格） `匹配0702/final_0702_mapping_w_grid/0702_full_v20260529_mapped.csv`<br>未匹配名单 `匹配0702/final_0702_non_map/0702_v20260529_non_map.csv`<br>执行日志追加|
| **人工审核**<br>把未匹配名单发给 于迪 审核正误并人工处理。 | 于迪将进行以下工作： <br>1. 返回人工审核匹配文件（包含机器匹配是否正确，机器匹配错误的再人工匹配到ESID) <br>2. 人工仍未能匹配的样本，添加到 药店主数据 表 |  | 把于迪的“返回人工审核匹配文件”存放于<br>`匹配0702/人工/已审核门店/0702_v20260529_non_map_返回.xlsx` |
| **4. 最终匹配结果**<br>`python code/step04_post_match.py --config config/config_0702.yaml` | 1. 吸纳当期人工审核返回，融合成新的历史审核。<br>2. 生成最终的当期全量药店匹配关系与映射网格。 | “返回人工审核匹配文件”已存放于指定位置<br>于迪已更新主数据 | 融合的历史审核结果 `匹配0702/人工/0702PharMapReviewed_v20260529.csv`<br>最终全量匹配结果 `匹配0702/final_0702_mapping_w_grid/0702_full_v20260529_mapped_new.csv` |
| **收尾**<br>两个文件需上传数据库<br>1. 最终全量匹配结果 `匹配0702/final_0702_mapping_w_grid/0702_full_v20260529_mapped_new.csv` --> 追加到：药店表.0702PharmacyMapping，注意不要清空原表 <br>2. 融合的历史审核结果 `匹配0702/人工/0702PharMapReviewed_v20260529.csv` --> 更新：药店表.0702PharMapReviewed，先清空原表，注意备份！ |  | 已运行以上步骤 | 药店表.0702PharmacyMapping 添加最新版样本匹配结果<br>药店表.0702PharMapReviewed 更新为最新融合的历史审核结果 |

---

## 脚本其他详细使用方法（以匹配0702为例）

所有脚本通过 `--config` 参数指定数据源，在项目根目录下运行：

```bash
# ===== 初始化：MF主数据下载+清洗（跨数据源共享，仅需在主数据更新时运行一次）=====
python code/step00a_master.py                           # 默认：下载 + 清洗 (需要约20分钟)
python code/step00a_master.py --download-only         # 仅下载（跳过清洗）
python code/step00a_master.py --clean-only            # 仅清洗（跳过下载，用已有csv）
python code/step00a_master.py --clean-only --test     # 仅清洗 + 测试模式（随机取1万行）

# ===== 0702 =====
python code/step01_download.py     --config config/config_0702.yaml   # 下载样本
python code/step02_clean.py        --config config/config_0702.yaml   # 增量清洗（默认）
python code/step02_clean.py        --config config/config_0702.yaml --mode full    # 全量清洗
python code/step03_match.py        --config config/config_0702.yaml   # 机器匹配 + 利用历史人工审核修正
# --no-review： 如果你只想单纯看机器匹配结果，不想用人工历史数据，可以加上 --no-review 参数跳过审核表

# --- （开发中，千问2b很傻别用，但高级的我电脑也跑不动）LLM辅助审核（替代人工）---
python code/step03b_llm_review.py  --config config/config_0702.yaml    # LLM审核non_map → 生成与运维返回文件格式一致的结果

# --- 人工审核返回后处理 ---
python code/step04_post_match.py   --config config/config_0702.yaml   # 人工审核表融合与网格重建（默认会强制从数据库下载最新主数据，以获取新门店的正确省份）
python code/step04_post_match.py   --config config/config_0702.yaml --skip-update  # 跳过主数据下载环节，直接使用本地现成数据以加速处理（风险：样本提供的省份是连锁总部所在省份，不一定是真正的物理地址。导致极少数门店的网格分配错误）
```

## 两个数据源的主要差异

| 参数 | 0701 | 0702 |
|------|------|------|
| ID字段 | `门店编码` | `pharmacy_id` |
| name构造 | 企查查名称优先 (`qcc`) | head+shortname拼接 (`concat`) |
| 信用代码 | ✅ 有（`统一社会信用代码`） | ❌ 无 |
| 城市字段 | ✅ 有（`城市`） | ❌ 无 |
| 匹配条件 | `con_0`~`con_6`（含信用代码匹配） | `con_1`~`con_6`（无信用代码） |
---

## 匹配条件定义 (Matching Conditions)

系统在机器匹配 (`step03`) 时，采用多级漏斗进行硬性条件过滤和最高分选取，优先级从 `con_0` 到 `con_6` 逐级降低：

- **`con_0`**: 统一社会信用代码 (`code`) 完全一致且不为空（目前仅 0701 数据源适用）。
- **`con_1`**: 待匹配数据的 连锁+门店组合名称 (`name`) 或 仅门店名称 (`shortname`) 与 MF 主数据的名称完全一致且不为空。
- **`con_2`**（已废弃）: 清洗提取的所有特征（去杂质字符串 `string_all`）完全一致且不为空。
- **`con_3`**: 清洗后的标准化地址 (`addr_clean`) 完全一致且不为空。
- **`con_4`**: 提取的连锁总店名称 (`head`) 相同，且门店特征数字 (`store_num`) 相同，且文本空间 Jaccard 相似度打分 >= 60。
- **`con_5`**: 门店特征数字 (`store_num`) 相同，且文本空间 Jaccard 相似度打分 >= 60（放宽连锁总部限制，避免实际同一门店但在双方处于不同连锁导致的匹配不上）。
- **`con_6`**: 兜底条件（无其他硬约束，完全基于匹配漏斗生成的 Jaccard 最高分数，由配置的 `cutoff` 阈值最终决定）。

---

## 新增数据源

1. 复制 `config_0701.yaml` → `config_0703.yaml`
2. 修改配置参数（表名、字段映射等）
3. 创建 `匹配0703/` 目录
4. 执行同一套脚本即可，**无需修改代码**

---

## 依赖

- Python 3.12
- pandas 2.1.4、numpy、sqlalchemy、pyyaml、tqdm、jieba
- 运行环境：`mapping` conda env

---

## Git 

### 1.同步与操作指南

**【远程仓库关联信息】**
- **远端平台**: 内部 GitLab (`gitlab.pharmcube.com`)
- **目标仓库**: `http://gitlab.pharmcube.com/data-hunter/PharmacyMapping.git`
- **身份验证**: 你的本地电脑已基于内部账号 `caoxuejing@pharmcube.com` 和凭证成功关联此远端中心仓库。因此之后操作时工具会自动定位到该库。

本项目已接通 Git 版本控制。因为远端 GitLab 开启了安全策略（保护主分支），小白开发者请严格按照以下流程进行日常的代码或配置同步：

### 2. 日常同步三步曲（请在终端执行）

当你在本地修改完任何文件后：

```bash
# 步骤 1：把所有修改加载到暂存区（系统会基于 .gitignore 自动无视掉不需要的大文件）
git add .

# 步骤 2：把暂存区打包提交到你电脑本地仓库，务必写上具体的任务说明
git commit -m "更新了: 你的修改内容简短描述"

# 步骤 3：推送到服务端的 dev（开发）分支（将本地的 master 往远程的 dev 推送）
git push origin master:dev
```

### 3. 如何完成最终的合并
1. 当你执行完 `git push` 命令给服务端发完代码后，请打开你的 GitLab 项目网页端。
2. 页面顶部会自动弹出一个明显的绿色提示栏 `You pushed to dev at ...`，请点击右侧的 **Create merge request** 按钮。
3. 在打开的新页面中，填写一下你这次修改的概括说明，检查无误后，点击绿色底按钮提交申请。
4. 在公司沟通群里 `@` 一下拥有合并权限的同事，让他帮你一键通过即可。

### 4. 哪些文件会被同步？
- 所有的代码 `.py`, 配置文件 `.yaml`, 和你的笔记文档 `.md` 都会被完整上传。
- 极大可能造成数据混淆的环境缓存、数据文件源、巨大的中间产物（例如 `output/`, `data_clean/`、`__pycache__/` 等等）**完全不被监控**。
- **最大的例外：`ref/` 参考配置文件夹**。（原本 `.gitignore` 保护机制会拦截你所有的 `.xlsx` 等文件，但由于该文件夹下的词典表是你项目的核心公共资产，此文件夹已被**强制接管开绿灯**，你在这里面修改任意 Excel 都能被随时同步更新并不会丢失）。
