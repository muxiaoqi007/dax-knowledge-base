---
title: "Teach Copilot your world with custom instructions"
url: "https://dax.tips/2026/05/25/custom-instructions-teach-copilot-your-world"
published: "2026-05-24"
updated: "2026-05-24"
source: "dax.tips"
---

![A glowing scroll of light unfurling against a dark background.](https://i0.wp.com/dax.tips/wp-content/uploads/2026/05/mrlovebucket_A_single_glowing_document_or_scroll_of_light_flo_53afb884-400c-4162-a16d-0b6ed65af9e7_0.png?resize=1000%2C560&ssl=1)

*Part of the [VS Code + GitHub Copilot as a Personal Assistant](https://dax.tips/?p=3543) series.*

If you take one thing from this series, take this one. Custom instructions are the single biggest upgrade you can make to GitHub Copilot, and most people never set them up.

A generic Copilot is helpful but neutral. It does not know who you work for, who your colleagues are, which customers you are engaged with, what your file structure looks like, how you write, or what you want to be reminded about. It guesses every time.

A Copilot with custom instructions knows all of that. It does not guess.

## What it is

A single markdown file at `.github/copilot-instructions.md` in your workspace. VS Code automatically prepends its contents to every chat in that workspace. You do not have to remember to mention it. You do not have to tell each new chat who you are.

It is the assistant equivalent of giving a new colleague a half-page brief on the team before their first meeting.

## What I put in mine

Mine is a few hundred lines and growing. The big sections are:

- **About me.** Name, role, location, expertise. Two or three sentences. Enough that the assistant knows what shape of help is useful.
- **Org chart.** Who my manager is. Who my manager’s manager is. Names of key product team contacts. Who the engineering leads are. Who runs the relevant adjacent teams. This sounds like overkill until the first time the assistant correctly maps a customer ask to the right engineering team without being told.
- **Workspace structure.** A short tour of the folders. What lives in `!CustomerFiles/`, what lives in `!WorkDiary/`, what lives in `!Presentations/`. So the assistant knows where to look without me telling it.
- **File type reference.** What a .dax file is. What a .daxx file is. What a .vpax file is. So when I drop one into chat the assistant immediately knows what kind of analysis is useful.
- **Conventions.** How I name files. How I structure customer profiles. How I keep the daily diary. So the assistant follows the pattern when it writes new entries.
- **Voice rules.** No em-dashes. No corporate fluff. UK/NZ spelling. So when it drafts an email or a blog post it does not need to be told these things every time.
- **Standing instructions.** The morning routine procedure. The end-of-day routine. The customer interaction post-processing checklist. So a single phrase triggers a fully structured response.

## Why it pays off

There are three compounding benefits.

**No context priming.** Every new chat starts already knowing the basics. I do not type “I’m Phil, I work on DAX at Microsoft, my colleague runs the engineering side of…” into a fresh chat. That information is already loaded.

**Consistency.** Every chat applies the same voice, the same conventions, the same standing routines. The diary entries written today look like the ones written last week. The customer profile updates follow the same shape. I do not need to remember to enforce that.

**Tribal knowledge becomes explicit.** A lot of what I “know” is actually in my head. Writing it down for the assistant forces me to articulate it. I have caught myself realising I have a convention I have never told anyone, including new team members. The instructions file has become useful as documentation, not just as input to the AI.

## What to put in vs what to leave out

A few things I have learned:

- **Stable information goes in.** Org chart, file structures, conventions, naming. Things that change rarely.
- **Volatile information goes in workspace files, not the instructions.** Today’s calendar, this week’s priorities, the current customer engagement. Those belong in `!Calendar/today.md` or the relevant customer profile. The instructions tell the assistant where to look for the volatile stuff.
- **Preferences go in.** Voice rules. Output format defaults. Things that should apply everywhere.
- **Big procedures get pulled out into Skills.** If a workflow is more than a paragraph, it becomes its own file under `.github/skills/`. The instructions reference it briefly. Skills get their own post in this series.

## How to start

You do not need to write the whole thing in one go. Mine started as twenty lines and has grown over months. A practical starting point:

1. Create `.github/copilot-instructions.md` in your workspace root.
2. Write three short sections: who you are, what your workspace contains, and three voice rules you actually care about.
3. Save. Open a new chat. Notice the difference.
4. Every time you find yourself repeating something to Copilot (“remember, I prefer…”), add it to the file.

After a couple of weeks the file is doing real work and you can start adding the org chart and the conventions.

## A warning

Custom instructions are loaded into every chat, which means they are part of every prompt’s context. They cost tokens. Do not stuff the file with everything you can think of. Keep it tight. The version of the file that is too short is recoverable. The version that is too long degrades every conversation.

## The mental model

A generic Copilot is a brilliant temp who joined your team this morning.

A Copilot with custom instructions is the same brilliant person two months in, who knows the team, the codebase, the customers, and the way you do things.

The temp version takes ten minutes of orientation per task. The two-month version starts working immediately. The difference between the two is one markdown file.

---

Next in the series: [Skills: packaged workflows the agent invokes itself](https://dax.tips/?p=3547).

The LinkedIn version of this tip went out on 16 May 2026.

0
0
votes

Article Rating
