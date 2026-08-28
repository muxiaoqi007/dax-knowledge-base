---
title: "Getting started: VS Code and Copilot from zero"
url: "https://dax.tips/2026/05/25/getting-started-vscode-copilot-personal-assistant"
published: "2026-05-24"
updated: "2026-05-24"
source: "dax.tips"
---

![A glowing scroll of light unfurling against a dark background.](https://i0.wp.com/dax.tips/wp-content/uploads/2026/05/mrlovebucket_A_single_glowing_document_or_scroll_of_light_flo_53afb884-400c-4162-a16d-0b6ed65af9e7_0.png?resize=1000%2C560&ssl=1)

*Part of the [[vscode-for-everything-that-isnt-code|VS Code + GitHub Copilot as a Personal Assistant]] series.*

The rest of this series describes patterns and habits I have built up over time. Several readers have come back with the same fair question: *“All very interesting, but how do I actually start doing this myself?”*

This post is the answer. It is the practical, step-by-step companion to the rest of the series. Fifteen minutes from a fresh machine to a working personal-assistant setup.

You do not need to be a developer. You do not need to know git. You do not need to write any code. The whole point of the series is that VS Code is being used as a productivity environment, not a coding tool.

## What you need before you start

- A laptop running Windows, macOS, or Linux. Any modern version is fine.
- A Microsoft work account, a personal Microsoft account, or a GitHub account. Any one of these can sign you into Copilot.
- A GitHub Copilot subscription. If your workplace uses Microsoft 365 with the Copilot licence, you may already have it. Check by asking your IT team, or just try signing in after step 3. If not, an individual subscription is currently around USD 10 per month. There is also a free tier with limits if you just want to try it out.

That is it. Anything else gets installed as you go.

## Step 1: Install VS Code

Go to <https://code.visualstudio.com/> and download the installer for your operating system. Run it. Accept the defaults. Launch VS Code when it finishes.

On first launch, you will see a Welcome tab. You can close it. The interface looks intimidating because it was designed for developers. Most of it does not matter for what we are doing.

## Step 2: Sign in

Click the little person icon in the bottom-left corner of the VS Code window. Sign in with whichever account gives you Copilot access. If you do not know, try your work Microsoft account first.

Signing in once does two things at once: it sets up settings sync (so your VS Code looks the same on other machines you sign into) and it carries over to the Copilot extension we are about to install.

## Step 3: Install the GitHub Copilot extension

Click the Extensions icon in the left sidebar (it looks like four small squares). In the search box that appears, type “GitHub Copilot”.

Install **GitHub Copilot** and **GitHub Copilot Chat**. They are listed as separate extensions but you want both.

After installing, look for a small Copilot icon in the bottom-right of the VS Code status bar. If it has a slash through it, click it and follow the sign-in prompts. If you do not already have a Copilot subscription, this is the point where it will tell you.

## Step 4: Pick a workspace folder

This is the folder that will hold all your notes, diaries, customer profiles, and everything else. Pick somewhere your laptop already syncs to the cloud. For most people that means **OneDrive**, **Dropbox**, or **iCloud Drive**.

Why this matters: when the same folder is on every device you use, the assistant on every device sees the same content. You will appreciate this within the first week.

Create a new folder inside OneDrive (or your cloud-sync folder of choice). Name it something you will recognise. Mine is called `!Customer Stuff` because the exclamation mark sorts it to the top of my OneDrive. Yours can be `Personal Workspace`, `Notes`, `Brain`, whatever you like.

## Step 5: Open the folder in VS Code

In VS Code, click **File → Open Folder** and navigate to the folder you just created.

You will see the folder name appear in the Explorer panel on the left. The folder is empty. That is fine.

## Step 6: Open the Copilot Chat panel

Press **Ctrl + Alt + I** on Windows or Linux, or **Cmd + Ctrl + I** on Mac. The Copilot Chat panel opens on the right side of the window. This is where you will spend most of your day.

Make sure the panel is set to **Agent** mode (there is a small dropdown at the top of the chat input). Agent mode lets Copilot read your files and make changes when you ask. Without it, you are limited to a question-and-answer pattern that misses most of what makes this useful.

## Step 7: The bootstrap prompt

This is the moment that turns an empty folder into something useful. Paste the following prompt into the chat. Edit the parts in angle brackets first. The whole point of the bracketed parts is to make you stop and describe yourself: the more concretely you fill them in, the more useful Copilot will be from the first chat.

```powerquery
I am setting up this VS Code workspace as a personal productivity environment.
I do not write code. I want to use this for things like customer or contact
notes, daily diary entries, meeting prep, project tracking, and reference
material.

Please scaffold a starting folder structure that makes sense for this kind of
work. Also create a `.github/copilot-instructions.md` file in the workspace
root that captures:

- My name: <your name>
- My role: <your job title, e.g. "Marketing director at a SaaS company",
  "Independent consultant", "PM at a manufacturer">
- My location and time zone: <city, country, time zone>
- The kind of work I do day to day: <2 or 3 sentences describing the actual
  shape of your week. Who you work with, what you produce, what you spend
  most of your time on. The more concrete the better.>
- Voice preferences for anything you draft on my behalf: <e.g. "plain
  English, no jargon, no em-dashes, UK spelling", or whatever rules you
  actually care about>
- A simple "good morning" routine: when I type the words "good morning", I
  want you to summarise today's date, list any meeting notes in
  the /Calendar/ folder, surface any open items from the most recent diary
  entry, and suggest a focus for the day.

Markdown for all content. Do not write code unless I specifically ask. Pick
a folder naming convention you think will work for me and explain it briefly.

```

Send the prompt. Watch Copilot create the folder structure. It will probably show you the files it is creating and ask you to approve them. Approve them.

You now have a workspace. There is a starting set of folders. There is a custom instructions file that already knows your basics. There is a morning routine you can trigger by typing two words.

## Step 8: Use it today

Do not wait for a perfect setup. Start using it on something real today.

A few starter exercises:

- **Start your diary.** Type *“Open today’s diary entry and add a few notes about what I am working on this morning.”* Watch Copilot create a file and start it for you. Update the file directly when you want, or via chat.
- **Add a contact or customer.** Type *“Set up a profile for [name of someone you work with regularly] in the appropriate folder. We last met on [date] and discussed [topic].”*
- **Try the morning routine.** Type *“Good morning.”* See what comes back. It will not be complete on day one, because there is not much for it to read yet. By day five it will start to feel useful.
- **Brief yourself before a meeting.** *“I have a meeting with X in an hour. Pull together anything we have on them and draft a one-page brief.”*

The point is to put real content into the workspace from day one. The assistant gets useful in proportion to how much real material it can read.

## What you can ignore for now

The rest of this series goes into specific patterns: chaining models, packaging skills, piping Power Automate, version control as an undo button, and so on. None of it is required on day one. You can read those posts in any order, and adopt the bits that fit. The starting setup above is enough to get genuine value within a week.

## Common gotchas

- **Copilot Chat panel keeps switching to a different mode.** Look for the mode dropdown at the top of the input. Stick to Agent mode while you are getting started.
- **Files are not syncing across devices.** Make sure the folder you picked is actually inside the OneDrive (or other cloud) folder, not just on your local disk.
- **The custom instructions file is being ignored.** Check it is at `.github/copilot-instructions.md` relative to the workspace root, not somewhere else. The dot at the start of `.github` is important.
- **The assistant invents things you did not put in.** Tell it not to. Add a line to your custom instructions: *“If you do not have a source for a piece of information, say so rather than guessing.”* This single line saves a lot of cleanup.

## What good looks like after a week

By the end of a week of normal use, your workspace will have a daily diary, a few contact or customer profiles, some notes, and probably a draft or two. The assistant will start to feel like it knows what you are doing without being told.

That is the moment the rest of the series starts to matter. Each post in the series gives you a specific pattern for getting more value out of what you have already built.

---

Back to [[vscode-for-everything-that-isnt-code|the series intro]] for the broader frame, or jump straight into the first specific tip: [[dont-run-everything-in-one-chat|Don’t run everything in one chat session]].

0
0
votes

Article Rating
