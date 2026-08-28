---
title: "Pipe Power Automate into your VS Code workspace"
url: "https://dax.tips/2026/05/25/pipe-power-automate-into-vs-code"
published: "2026-05-24"
updated: "2026-05-24"
source: "dax.tips"
---

![A glowing scroll of light unfurling against a dark background.](https://i0.wp.com/dax.tips/wp-content/uploads/2026/05/mrlovebucket_A_single_glowing_document_or_scroll_of_light_flo_53afb884-400c-4162-a16d-0b6ed65af9e7_0.png?resize=1000%2C560&ssl=1)

*Part of the [VS Code + GitHub Copilot as a Personal Assistant](https://dax.tips/?p=3543) series.*

There is a limit to how useful an assistant can be when it only knows what you remembered to tell it. If you forget to mention that the customer you are about to meet sent a frustrated email overnight, no amount of cleverness in the chat will compensate. The information has to be present.

Power Automate solves this for free.

## The pattern

A Power Automate flow triggers on a real-world event. *“A new calendar event was added”, “An email arrived in this folder with this category”, “A message was posted in this Teams chat”.* The flow runs and writes a small markdown or JSON file into a known folder in my OneDrive-backed workspace.

Copilot, reading the workspace, treats those files exactly like any other file. The information is there. No prompting required.

## What I pipe in

Three things, all running quietly in the background.

**Outlook calendar, every four hours.** A flow reads the next two weeks of my Outlook calendar and writes the events to `!Calendar/upcoming.json`. A separate flow generates `!Calendar/today.md` for the day’s events in human-readable form. When I ask my morning routine to brief me on the day, the routine reads these files. I do not need to forward the calendar manually or paste it into chat.

**Selected emails.** A flow watches specific Outlook categories and folders. When an email matches, the flow drops a `.eml` file into `!Emails/input/`. My email-processing Skill picks them up from there, decodes them, matches them to customers, and updates the relevant profiles. I do not have to manually save emails or summarise them. They appear, they get processed, they end up in the right customer file.

**Teams chat threads.** A flow per chat or channel. When a new message arrives in a thread I care about, the flow appends it to a markdown file in `!Transcripts/teams-channels/`. Active threads I have piped: technical engineering discussions, customer-specific investigations, my fortnightly with one of the product leads, my weekly 1:1. Every relevant conversation accumulates as a chronological file I can search, reference, or have the assistant summarise.

There is also a workspace auto-sync that commits and pushes to GitHub every thirty minutes. Not Power Automate strictly, but the same principle. Background automation, plain files in the workspace, the assistant sees everything.

## Why this matters more than it sounds

Three reasons.

**The assistant becomes proactive without being annoying.** When I ask for a morning briefing, the assistant already has my calendar, the overnight emails, and any Teams updates. The briefing is concrete. It mentions the customer I am meeting at 11, the email they sent at 23:00 my time, and the action item from yesterday’s chat. None of that required me to type any of it in.

**Customer context is always current.** When a customer email arrives, the email-processing skill updates the customer profile within minutes. Next time I need to brief myself on that customer, the profile reflects what they actually told me yesterday, not what I remembered to write down a fortnight ago.

**Chat thread continuity survives across sessions.** Teams chats are normally trapped inside Teams. When the assistant cannot see them, every reference has to be reconstructed from memory. With the chat file synced into the workspace, the assistant can read the full chronology and pick up where the human conversation left off.

## How to set one up

You do not need to build all of mine on day one. Start with one flow that captures one thing.

The simplest pattern is the calendar feed:

1. In Power Automate, create a recurrence-triggered flow (every four hours is fine).
2. Action: *Get calendar view of events V3*. Point it at your primary calendar. Time window two weeks ahead.
3. Action: *Create file*. Connector: OneDrive for Business. Target: the `!Calendar/` folder in your workspace. Filename: `upcoming.json`. Content: the events from step 2, optionally trimmed.
4. Save. Run once manually. Confirm the file appears in your workspace.

The first time you see the file appear in VS Code, it feels mildly magical. After a week it feels obvious.

## Practical tips

- **Use plain markdown or JSON.** Both are trivially readable by the assistant. Avoid HTML, avoid Office formats, avoid anything binary. The whole point is that the file is human and machine readable.
- **Trim before writing.** Calendar events contain a lot of metadata you do not need. Strip it down to subject, start time, end time, organiser, location, body, and the Teams join URL. Smaller files load faster into context.
- **One file per source.** Do not pile everything into a single mega-file. The calendar gets its own file. Emails get their own folder. Each Teams thread gets its own file. The assistant can read what is relevant without dragging unrelated content into context.
- **Make the file structure predictable.** The assistant references files by path. If the path is stable, the assistant can keep using the same reference indefinitely. If you keep moving files around, you make life harder for both you and the assistant.
- **Document the pipeline.** I have a `README.md` in `!Transcripts/teams-channels/` that explains which flow writes to which file. When something breaks at 11pm on a Friday, that README is what saves me.

## What this is not

This is not about replacing Outlook, Teams, or your real apps. The originals stay where they are. Power Automate just makes a parallel copy of the bits the assistant needs to see.

It is also not about real-time. Most of my flows run every four hours. Some run on event triggers and arrive within a minute or two. Neither is instant. Neither needs to be. The point is that by the time I ask the assistant a question, the information is already present.

## The mental model

A regular AI assistant is a colleague who only knows what is in front of them at this moment.

A piped-in assistant is a colleague who has been quietly reading your calendar, your inbox, and your Teams chats all day, and is waiting for you to ask.

Same person. Vastly different briefing.

---

Next in the series: [The morning routine: a single phrase that briefs you](https://dax.tips/?p=3549).

The LinkedIn version of this tip went out on 19 May 2026.

0
0
votes

Article Rating
