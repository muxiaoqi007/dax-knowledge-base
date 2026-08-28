---
title: "VS Code for everything that isn’t code"
url: "https://dax.tips/2026/05/25/vscode-for-everything-that-isnt-code"
published: "2026-05-24"
updated: "2026-06-08"
source: "dax.tips"
---

I am a Microsoft employee in the Customer Advisory Team. I work on DAX, semantic models, performance, Microsoft Fabric. My job is field-facing. Customer engagements, briefings, presentations, internal coordination, the occasional bit of writing for this blog.

Almost none of my day is writing software.

What it *is*: customer notes, meeting prep, email drafting, transcript processing, diary entries, presentation outlines, decision journals, expense triage, travel admin, product feedback writeups, weekly 1:1 agendas. The full surface area of a knowledge worker’s life.

For the last twelve months or so, all of that has been happening in VS Code with GitHub Copilot. Not the IDE I open *sometimes*. The IDE I open in the morning and close at the end of the day.

If that sounds odd, it is because the pitch for VS Code is almost always about code. The pitch for GitHub Copilot is almost always about code. Neither tool’s marketing tells you what they are really capable of once you stop using them for that.

## What the shift actually feels like

The fastest way to explain it is by example.

When I sit down in the morning, I type one phrase into the chat. A structured routine fires off. It reads my calendar for the day, summarises emails that arrived overnight, surfaces carry-forward items from yesterday’s diary, lists active projects with their blockers, and gives me a single briefing. Then it suggests a focus for the morning. I have a coffee while I read it. The whole thing takes me about three minutes.

When I come out of a customer call, I drop the transcript file into a folder. The assistant reads it, identifies which customer it was, updates the customer’s profile with what was discussed, captures decisions and action items into today’s diary, flags anything that should become product feedback for the engineering teams, and notes anything worth raising in my next 1:1. I do nothing except move the file.

When I am preparing for a conference talk, I keep all the slides, demos, supporting notes, and audience-feedback artifacts in the same workspace as the rest of my life. The assistant can read across all of it. *“Pull together everything I’ve learnt from customer engagements about Direct Lake performance over the last six months and turn it into talking points for the FabCon session.”* It does it.

When I am on the road, the whole workspace is on my phone via OneDrive and GitHub. Same files. Same minute. The mobile assistant reads the same diary the desk assistant wrote.

None of this is special. None of it required custom development. It is the standard tools, used differently.

## What this series is going to cover

I am going to write up the setup as a series of short, focused posts. One LinkedIn tip, one blog post. Each one stands alone. The early ones cover the basics. The later ones get into the specifics.

The first twelve posts are written and live. Click through to whichever one catches your eye:

1. **[[dont-run-everything-in-one-chat|Don’t run everything in one chat session]].** Multiple topic-based chats in parallel beats one monolithic conversation. Tighter focus, easier to find things weeks later.
2. **[[chain-ai-models-different-vendors|Chain AI models from different vendors to catch blind spots]].** Hypothesis from one model, critique from another. Different training data means different blind spots.
3. **[[custom-instructions-teach-copilot-your-world|Teach Copilot your world with custom instructions]].** A single file that loads into every chat. The single biggest unlock I have found.
4. **[[copilot-skills-packaged-workflows|Skills: packaged workflows the agent invokes itself]].** Procedures the agent pulls in automatically when you mention the trigger phrases.
5. **[[pipe-power-automate-into-vs-code|Pipe Power Automate into your VS Code workspace]].** Calendar, emails, Teams messages all landing as plain files the assistant can read.
6. **[[copilot-morning-routine|The morning routine: a single phrase that briefs you]].** Two words triggers a structured briefing of the day.
7. **[[copilot-subagents-do-the-dirty-work|Subagents: send the intern to do the legwork]].** Read-only sub-chats that do the heavy reading and report back, keeping your main chat focused.
8. **[[git-as-the-undo-button|Git as the undo button for everything you write]].** Auto-commit every thirty minutes so you can experiment fearlessly.
9. **[[chat-history-searchable-knowledge-base|Your chat history is a searchable knowledge base]].** The JSONL files VS Code stores on disk are a personal corpus.
10. **[[same-workspace-on-every-device|The same workspace on every device]].** OneDrive plus VS Code plus the mobile Copilot app means the same files are on every machine you touch. The continuity is the product.
11. **[[teach-ai-to-sound-like-you|Teach AI to sound like you, not like AI]].** Em-dashes, “I’m excited to”, “leverage” as a verb. A small voice-style file teaches the assistant to sound like you instead. Half an hour once, every email cleaner forever.
12. **[[choose-your-model-wisely|Choose your model wisely: premium for design, cheap for execution]].** Different models for different jobs. Premium reasoning model for the hard thinking, cheaper model to execute once the plan is locked. Stop sending the senior partner to file the paperwork.

More will follow as the LinkedIn series continues. The blog series is open-ended on purpose. New posts get added as the setup evolves.

## Why now

There are a few reasons.

The first is that the model of “AI as a typing assistant for code” is now badly out of date. The agentic capabilities in Copilot, the multi-vendor model support inside VS Code, the chat history persistence, the skill packaging, the subagent pattern, the integration with the local filesystem, the integration with Power Automate via plain files: all of this has converged in the last year. The toolkit available to a knowledge worker today is enormously more powerful than what was available even six months ago. Not enough people have noticed.

The second is that I keep talking to colleagues and customers who are doing some of this and want to compare notes. The LinkedIn comments are the same. People have built similar setups in Claude Code, in Cursor, in Roo, in their own bespoke harnesses, and the comparisons are genuinely useful. None of us has the complete picture. The point of writing it down is to make those comparisons easier.

The third is that this is the most fun I have had with computers in twenty years. It feels worth sharing.

## What you can expect

Practical, not aspirational. I will show what I actually run, not what the demo videos promise. I will admit where things break. I will name the tools and configurations by their real names. I will share the patterns that worked and the ones that did not.

I will publish roughly one of these per week for as long as there is something worth writing.

If you have built something similar with a different tool, I would genuinely like to hear about it. Comparisons across tools are where the most useful patterns surface.

Next post in the series: [Don’t run everything in one chat session](https://dax.tips/?p=3544). Why I use multiple topic-based chats in parallel instead of one monolithic conversation, and what that changes.

See you there.

5
1
vote

Article Rating
