---
title: "Skills: packaged workflows the agent invokes itself"
url: "https://dax.tips/2026/05/25/copilot-skills-packaged-workflows"
published: "2026-05-24"
updated: "2026-05-24"
source: "dax.tips"
---

![A glowing scroll of light unfurling against a dark background.](https://i0.wp.com/dax.tips/wp-content/uploads/2026/05/mrlovebucket_A_single_glowing_document_or_scroll_of_light_flo_53afb884-400c-4162-a16d-0b6ed65af9e7_0.png?resize=1000%2C560&ssl=1)

*Part of the [VS Code + GitHub Copilot as a Personal Assistant](https://dax.tips/?p=3543) series.*

In the [previous post](https://dax.tips/?p=3546) I described custom instructions: a single file that tells Copilot who you are, what your workspace looks like, and what conventions you follow. That handles the broad context.

The next layer up is Skills. A Skill is a packaged workflow. You describe a procedure once, save it as a file with trigger phrases, and from then on the agent invokes it whenever you say the trigger.

## Where Skills live

In your workspace, under `.github/skills//SKILL.md`. Each skill is its own folder. The SKILL.md file is the procedure. The folder can contain supporting files: templates, helper scripts, reference data.

The Copilot agent reads the available skills and decides on its own which one is relevant for the current request. You do not call them explicitly. You say the thing, and if a Skill matches it kicks in.

## What goes in a SKILL.md

The shape that works for me:

- **A short description at the top.** What this Skill does, in two or three sentences.
- **Trigger phrases.** The words and phrases that should pull this Skill into the conversation. *“Process emails”, “FATCAT submission”, “audit my product areas”.*
- **When NOT to use it.** Some Skills should be conservative. Mine for FATCAT product feedback explicitly says “do not auto-invoke this skill, only run when Phil explicitly asks”. Without that line, the agent would try to be helpful and log feedback every time I mentioned a product complaint in passing.
- **The procedure itself.** Step by step, in plain English. Sometimes with code blocks for the actual commands. Sometimes with decision trees.
- **Dependencies.** Other files the Skill reads or writes. Scripts it shells out to. Templates it pulls from.

## My current Skills

I have four right now. They evolve.

- **process-emails.** Triggered by emails landing in `!Emails/input/` or me saying “process emails”. Decodes the MIME, matches each email to a customer (by category, by alias, by folder name), updates customer profiles, flags potential product feedback, archives the original. The whole pipeline.
- **fatcat-feedback.** Triggered by “submit PF-022 to FATCAT” or similar. Handles the deduplication check, the customer account lookup, the Issue lookup or creation, the Issue Log creation, and the submission log update. Includes guardrails about never auto-invoking, never inventing customer accounts, and always confirming before POST.
- **fatcat-product-areas.** Audits the FATCAT Product Areas I own. Reassigns misassigned Issues. Used during scheduled audit passes.
- **post-interaction.** Triggered after any customer interaction. Captures the customer profile update, the daily diary entry, the FATCAT feedback flag if applicable, and the cross-references with other ongoing work.

Each one started as me repeating the same steps in chat. After the second or third time I noticed I was typing the same instructions over again, I pulled the procedure out into a Skill. From that point the agent runs it correctly every time.

## Why this matters

Skills do three things that custom instructions cannot.

**Procedure lives outside the chat.** When you fix or improve the procedure, every future chat inherits the improvement. You do not have to remember to re-explain the workflow.

**Guardrails are enforced consistently.** The “never auto-invoke” line in the FATCAT skill is honoured by every chat. Without that line, I would have logged thousands of false positives by now.

**The agent invokes Skills on its own.** This is the bit that surprises people. You do not need to remember which Skill to call. You say the words, the agent matches them to a trigger, the Skill activates. The procedure runs whether or not you remembered it existed.

## Practical tips

A few things I have learned the hard way:

- **Start with the procedure you find yourself repeating.** Not the procedure you wish you had. The signal that a Skill is needed is the third time you type the same instructions.
- **Be explicit about NOT triggering.** It is easy to write triggers that are too eager. Bias the language towards explicit invocation. The cost of a missed trigger is small. The cost of an over-eager trigger is silent corruption of your records.
- **Include “what to do when this goes wrong”.** What if the customer is not in the workspace? What if the email format is unexpected? What if the API call returns an unexpected status? Spell out the recovery path so the agent does not improvise.
- **Reference files explicitly.** “Read `!Emails/input/.eml`” is better than “read the email”. Be specific about paths. The agent will follow them.
- **Skills can shell out to scripts.** If the procedure includes a PowerShell or Python step, the Skill can call it. The Skill is the orchestration. The script is the heavy lifting. Keeps each piece simple.

## How Skills relate to custom instructions

The instructions file is the standing brief. The Skills are the procedures. The instructions tell the agent who I am, what the workspace contains, and what voice rules to follow. The Skills tell the agent how to perform specific recurring tasks.

When the agent needs to act, it loads the instructions plus any relevant Skill, and goes. Neither file alone would be enough. Together they turn a chat assistant into something that genuinely operates inside your work.

## The mental model

Custom instructions are like the onboarding doc you give a new hire.

Skills are like the runbooks the team has built up over time. *“This is how we onboard a customer. This is how we process incoming feedback. This is how we close out a quarter.”*

A new hire with onboarding plus runbooks gets to value fast. A new hire with just onboarding spends weeks bumping into the same questions.

Same here.

---

Next in the series: [Pipe Power Automate into your VS Code workspace](https://dax.tips/?p=3548).

The LinkedIn version of this tip went out on 18 May 2026.

0
0
votes

Article Rating
