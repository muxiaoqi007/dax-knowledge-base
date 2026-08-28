---
title: "Git as the undo button for everything you write"
url: "https://dax.tips/2026/05/25/git-as-the-undo-button"
published: "2026-05-24"
updated: "2026-05-24"
source: "dax.tips"
---

![A glowing scroll of light unfurling against a dark background.](https://i0.wp.com/dax.tips/wp-content/uploads/2026/05/mrlovebucket_A_single_glowing_document_or_scroll_of_light_flo_53afb884-400c-4162-a16d-0b6ed65af9e7_0.png?resize=1000%2C560&ssl=1)

*Part of the [VS Code + GitHub Copilot as a Personal Assistant](https://dax.tips/?p=3543) series.*

This post is about the smallest piece of infrastructure in my setup that has had the biggest behavioural impact. It is also the one that feels most obvious to a developer and most novel to a knowledge worker.

Git.

Not git for the source code I rarely write. Git for everything else. Customer notes, diary entries, presentation outlines, briefings, drafts, decisions.

## The setup

A tiny PowerShell script in my workspace. Windows Task Scheduler runs it every thirty minutes. The script does three things and is about twenty lines long.

- Check if there is anything new to commit. If not, do nothing.
- If yes, stage everything, commit with a generated message, push to GitHub.
- Log success or any errors to a local file.

I have not touched it in months. It just runs.

The workspace is in OneDrive (covered later in the series). It is also a git repository with a remote on GitHub. So I have two parallel backups running constantly. OneDrive handles live sync across my devices. Git handles version history and remote backup.

## What it changes

This is the part that is hard to explain until you have lived with it.

The cost of trying something risky in the workspace drops to zero. *“Let Copilot rewrite this entire customer profile in customer voice.”* Sure. If it goes badly, the previous version is one git command away. *“Restructure six months of diary entries by week instead of by day.”* Yes, try it. If it makes things worse, revert. *“Generate ten draft emails and pick the best one.”* Fine. If none of them are usable, throw them all away.

Before this, I would hover over destructive-looking operations. After this, I do not. The undo button is always there.

## The side effects nobody warns you about

Three things I did not plan for.

**My phone has the entire workspace.** GitHub mobile plus a git client on iOS means every customer note, every meeting brief, every old chat is accessible from the airport gate. The OneDrive app provides the same thing through a different path. Two ways in. Either one works.

**Annual review prep is trivial.** Every accomplishment, every decision, every customer outcome from the year is in commit history. Time-stamped. Diffable. Searchable. When it is time to write up the year, I can rebuild any week’s narrative from the commits and the files they touched. This alone saved me about a day of work this year.

**Accidental deletes recover in under thirty minutes.** When something gets deleted (by me, by an automation, by a script with a buggy regex), the worst case is the previous half hour of work. Usually less. Git is the safety net.

## Why thirty minutes

Long enough that the commits are not constant noise. Short enough that you never lose much. I tried fifteen, found it too chatty. Tried an hour, found it lost too much when something went wrong. Thirty was the sweet spot.

The thirty-minute cadence also means a normal working day has somewhere between ten and twenty commits. That is enough granularity for “find the version from before lunch” to be a meaningful query.

## What the commit messages look like

Generated. Short. Indicative.

> auto-sync: 2026-05-25 14:30 (3 files)

Not informative on their own. Useful in combination with the file diff. If I want to know what changed at 14:30 last Tuesday, the diff tells me. The message just tells me when.

For the rare commits I make manually (when I want a real message attached to a particular change) I just do those by hand. The auto-sync coexists fine with manual commits.

## What I do NOT commit

The gitignore file is worth getting right.

- **Secrets.** Anything matching `*.secret` is ignored. API keys, tokens, app passwords. None of those go to GitHub.
- **Temporary scratch files.** A `scratch/` folder I use for one-off experiments. Nothing in there is worth keeping.
- **Large binary outputs.** Generated reports, exported data dumps. The originals are reproducible. The git repo stays small.
- **Other people’s data.** If a customer ships me a `.pbix` file or a data extract, I will keep it locally but not in the git repo. The boundary between “things I produce” and “things customers send me” is worth maintaining.

## Practical tips

A few things that mattered.

- **Use a private GitHub repo for your workspace.** This is your life. It does not belong on a public one.
- **Skip git hooks for the auto-sync.** The auto-sync should never fail because some linter complained. Pre-commit hooks are useful elsewhere, not here.
- **Keep the script idempotent and silent on success.** When the script runs every thirty minutes, you do not want a notification every time. You want it to be invisible until something is wrong.
- **Log errors to a local file.** When something does go wrong (network blip, expired GitHub credentials, disk full) you want a quiet log to check, not a dialog box.
- **Test the recovery path.** At least once, deliberately delete a file you care about and recover it from git. The first time you do this, you will trust the system. Until you do, you do not.

## The mental model

A regular workspace is a single physical artefact. If you lose it, you lose the thing. So you handle it carefully, you avoid changes you might regret, you take fewer risks.

A workspace with auto-commit is a thing with infinite history. You cannot really lose the thing. So you handle it cheaply, you take more risks, you experiment more freely. The behaviour change is what matters. The git plumbing is a tool. The fearless experimentation is the product.

## Why this works especially well with Copilot

This is the bit that brings it home for a Copilot user. AI assistants are most useful when you let them try things. Let it rewrite the customer profile. Let it restructure the briefing. Let it propose ten variants and you pick one.

Without an undo button, each of those operations is a small risk. You think twice before granting them.

With an undo button, you grant them freely. The good ideas land. The bad ones get reverted. You end up with a much better artefact than you would have produced under your own steam, and you spent less of your own steam getting there.

The git auto-commit is the difference between using Copilot timidly and using it fully.

---

Next in the series: [Your chat history is a searchable knowledge base](https://dax.tips/?p=3552).

The LinkedIn version of this tip went out on 22 May 2026.

0
0
votes

Article Rating
