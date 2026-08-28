---
title: "Subagents: send the intern to do the legwork"
url: "https://dax.tips/2026/05/25/copilot-subagents-do-the-dirty-work"
published: "2026-05-24"
updated: "2026-05-24"
source: "dax.tips"
---

![A glowing scroll of light unfurling against a dark background.](https://i0.wp.com/dax.tips/wp-content/uploads/2026/05/mrlovebucket_A_single_glowing_document_or_scroll_of_light_flo_53afb884-400c-4162-a16d-0b6ed65af9e7_0.png?resize=1000%2C560&ssl=1)

*Part of the [VS Code + GitHub Copilot as a Personal Assistant](https://dax.tips/?p=3543) series.*

Most chat assistants do their reading in the same conversation you are having with them. You ask a question, they go and read fifty files, every one of those reads enters the current context, and by the time they answer your question the conversation has lost track of the topic you were actually on.

There is a better way.

## What a subagent is

A subagent is a read-only sub-chat that your main chat spawns to investigate something. You describe what you want investigated. The subagent goes off, reads whatever it needs to read, does whatever search and analysis the task requires, and comes back with a single focused report.

The work the subagent does happens in its own context. Your main chat never sees the fifty files. It just gets the answer.

The main chat keeps its memory.

## Why this matters

A chat session has a finite context window. Every file read, every tool output, every long block of search results consumes that window. The longer a chat runs, the more crowded the context becomes. The earliest things you said get evicted or summarised away.

If you do all your investigation work in the main chat, you trade away the conversation’s memory for the answer to one question. Sometimes that trade is fine. Often it is not.

A subagent does the investigation in a fresh, dedicated context. The result that comes back is concise. Your main chat absorbs that, not the underlying noise.

## How subagents get invoked

Three ways, easiest first.

**Implicit.** In agent mode, Copilot will often decide on its own that a task is suitable for delegation. *“Search every customer profile for mentions of bitmap indexes and tell me which customers raised it”* will usually trigger a subagent without you having to think about it. The agent recognises the shape of the task.

**Explicit nudge.** If you want to be sure, ask. *“Spin up a subagent to read these files and report back.”* Hard to misinterpret. The agent will delegate.

**Named specialist agents.** You can configure custom subagents for recurring patterns. They live in the workspace as additional configuration. Each one has a description that tells the main agent when to route to it. I have a couple. One for fast codebase searches. One for the customer-feedback work I do. When I say the name, Copilot routes directly to that specialist.

## What I actually use them for

Three patterns covered most of my subagent use last week.

**Cross-file search and synthesis.** *“Search every customer profile for mentions of bitmap indexes and tell me which customers raised it.”* The subagent reads about eighty files, returns a list with a one-line summary per match. My main chat never sees the files. It just gets the answer and we keep going with whatever I was doing.

**Dependency tracing.** *“Trace through this DAX query and tell me which measures it depends on, recursively.”* The subagent walks the dependency tree, reads each measure definition, builds the map. Comes back with a clean tree. The detail does not pollute the main chat.

**Long document synthesis.** *“Read this customer’s last six diary entries and summarise the open threads.”* The subagent does the reading. I get the synthesis. The main chat keeps its head clear.

## The mental model

The main chat is you. The one having the actual conversation. The one making decisions. The one whose context matters.

A subagent is the researcher you would hire if you had one. Cheap. Fast. Doesn’t get bored. Doesn’t fill your inbox with progress updates. Comes back with the answer.

This is the same instinct as why human knowledge workers use research assistants, junior analysts, paralegals. You do not personally read every supporting document. You delegate the reading and absorb the result. Subagents make that delegation pattern available to a single human.

## Practical tips

A few things that took me a while to figure out.

- **Be specific about the deliverable.** *“Tell me which customers raised this”* is good. *“Look into this for me”* is bad. The subagent will read more than it needs to if you do not constrain the output.
- **Be specific about the scope.** *“Read every customer profile under `!WorkDiary/customers/`“* is much better than *“read the customer profiles”*. Paths beat vibes.
- **Subagents are read-only by default for a reason.** Don’t grant them write access unless you specifically want to. The investigation pattern depends on the main chat staying in control of changes.
- **Subagent output is just text.** Treat it like any other input. You can ask follow-up questions about it in the main chat. You can ask the main chat to act on it. You can send the output back into a different subagent for a second pass.
- **Use named specialist agents when the same investigation shape comes up repeatedly.** The setup cost pays back fast.

## When NOT to use a subagent

A subagent has overhead. Spinning one up, waiting for the report, reading it: this is more elapsed time than just asking the main chat. For small tasks (read one file, summarise it) the subagent is overkill. Use them when the underlying work is genuinely larger than the answer you need.

The other case where subagents are wrong: when you actually want the detail in the main chat’s context. *“Read this contract and let’s discuss it in detail”* is a job for the main chat, not a subagent. You want the back-and-forth on the substance.

## What this changes about how I work

The shift is subtle but real. Before subagents, I avoided asking the assistant to do anything that involved reading more than a handful of files, because I knew the answer would come at the cost of the conversation’s coherence.

After subagents, that hesitation is gone. *“Tell me which of my eighty customers have ever asked about bitmap indexes”* is a fine thing to ask. The subagent handles it. The main chat is still focused on whatever we were doing.

The result is that I lean on the assistant for things I would not have bothered to ask before. The asking cost dropped.

---

Next in the series: [Git as the undo button for everything you write](https://dax.tips/?p=3551).

The LinkedIn version of this tip went out on 21 May 2026.

0
0
votes

Article Rating
