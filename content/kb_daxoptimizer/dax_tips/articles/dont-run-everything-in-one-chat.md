---
title: "Don’t run everything in one chat session"
url: "https://dax.tips/2026/05/25/dont-run-everything-in-one-chat"
published: "2026-05-24"
updated: "2026-05-24"
source: "dax.tips"
---

![A glowing scroll of light unfurling against a dark background.](https://i0.wp.com/dax.tips/wp-content/uploads/2026/05/mrlovebucket_A_single_glowing_document_or_scroll_of_light_flo_53afb884-400c-4162-a16d-0b6ed65af9e7_0.png?resize=1000%2C560&ssl=1)

*Part of the [VS Code + GitHub Copilot as a Personal Assistant](https://dax.tips/?p=3543) series.*

The intuition with most chat tools is to use a single ongoing conversation. The same chat window stays open. You ask it about code, then about a customer, then about an email, then back to code. The thinking is that the assistant builds up context over time and gets better the longer you talk to it.

In practice the opposite happens.

## The problem with one monolithic chat

A chat session is bounded by a context window. Every message, every file the assistant reads, every command output it sees, all of it sits inside that window. When the window fills up, older content gets squeezed out or summarised away.

The longer a chat runs across multiple topics, the more two things happen:

1. The assistant loses track of which topic it is currently on, and starts blending advice from one topic into another.
2. The earliest parts of the conversation get evicted, including the ones that set up your preferences, your tone, your project conventions.

You can feel this. The replies get vaguer. The assistant starts asking questions it already had the answer to. It suggests something for project A that was actually about project B. The chat is no longer the asset you thought it was.

## What I do instead

I run multiple chat sessions in parallel, each scoped to a topic.

- **Daily Ops.** Fresh every morning. Routine briefing, email triage, calendar, quick tasks. Disposable. I close it at the end of the day.
- **Engineering 1:1.** Persistent. Technical deep-dives, customer pattern analysis, weekly prep for the technical 1:1 with one of my engineering colleagues. The context here is precious. It accumulates over weeks.
- **Customer: .** Spun up when I am running a multi-session investigation on a single customer. Closed when the engagement wraps up.
- **FATCAT & Feedback.** Product feedback work. Customer issue logging. Cleanup passes on the feedback database.
- **Blog drafts.** This one. Open when I am writing, closed when I am not.

Each chat is renamed in VS Code with a purpose, not a date. *“Engineering 1:1”*, not *“Chat 2026-05-25 14:31”*. That way I can jump between them by name.

I have somewhere between three and seven chats open on any given day. I switch between them by clicking. VS Code keeps them all alive.

## What it changes

Two things, both bigger than they sound.

**Token efficiency.** Each chat only loads the context relevant to its topic. The engineering chat does not need to know about my email triage. The blog chat does not need to know about the FATCAT cleanup. Tighter focus means faster, more accurate replies.

**Review value.** Weeks later, the per-topic chats are easier to find and quote. If I want to remember what we decided about a particular customer issue, I open the Customer chat. If I want last fortnight’s technical discussion with my engineering lead, that chat is still right there. There is no monolithic transcript to grep through.

## The mental model

A single chat session is a single conversation. If you tried to have one continuous conversation with a colleague across every topic, you would also lose the thread. You do not. You have one conversation about the customer, another about the budget, another about the team. Each one starts and ends. Each one stays focused.

Treat chat sessions the same way.

## Practical tips

A few things that took me a while to figure out:

- **Rename chats early.** The default name is useless. Renaming takes two seconds and pays off every time you switch.
- **Close chats when they are done.** Not every chat needs to live forever. The Daily Ops chat is gone by the next morning. Liberating.
- **Cross-chat references work.** Every VS Code chat saves to a JSONL file on disk under `%APPDATA%\Code\User\workspaceStorage\...\GitHub.copilot-chat\transcripts\`. You can paste a path into a different chat and say “pick up where we left off here”. The new chat reads the transcript and absorbs the context. This is its own tip and gets its own post later in the series.
- **The shared workspace is the connective tissue.** When something needs to flow between chats, it goes through a file in the workspace. Customer profile updates, diary entries, decisions. Each chat reads and writes those files. None of them need to be in the same conversation to stay coordinated.

## What this is not

This is not about running multiple chats with the same prompt to compare answers. That is a different tip. *(The next one in the series, in fact.)*

This is about running parallel conversations on different topics so each one keeps its head clear.

---

Next in the series: [Chain AI models from different vendors to catch blind spots](https://dax.tips/?p=3545).

The LinkedIn version of this tip went out on 14 May 2026. Find me there for the short version of each post, and the blog for the longer one.

0
0
votes

Article Rating
