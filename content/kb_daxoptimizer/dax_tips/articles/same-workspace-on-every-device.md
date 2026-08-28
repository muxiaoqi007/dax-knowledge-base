---
title: "The same workspace on every device"
url: "https://dax.tips/2026/05/26/same-workspace-on-every-device"
published: "2026-05-25"
source: "dax.tips"
---

My entire VS Code workspace is a single folder inside OneDrive. That is it. No fancy syncing engine. No bespoke backup tool. The folder lives in OneDrive on my main work laptop. OneDrive on my second laptop syncs the same folder down. OneDrive on my phone shows me the same files. The browser version of OneDrive lets me read them from any machine I happen to be at.

The folder contains everything. Customer notes, diaries, presentations, briefings, scripts, drafts, the lot. Nothing important lives outside it.

Git is the second layer of safety on top (covered in [[git-as-the-undo-button|yesterday’s post]]). OneDrive handles live sync. Git handles history. The two together make the workspace a continuous, recoverable, ever-present thing.

## What this changes

Three things, in order of how much they end up mattering.

**The mobile assistant reads the same files the desk assistant wrote.** This is the headline. When I update a customer profile on my laptop in the morning, the GitHub Copilot mobile app on my phone reads the updated profile in the afternoon. The continuity is genuine. The phone is not a degraded copy. It is the same workspace, accessed from a smaller screen.

**Travel stops being a planning exercise.** I do not pack files. I do not export anything. I do not email myself documents. I land in a hotel, open my work laptop, and everything is there. The next morning I open my personal laptop in a different time zone and everything is there too. The friction of “which device is the file on” is gone.

**Recovery from device failure is trivial.** Every device is a cache of the workspace, not the canonical copy. If a laptop dies, I get a new one, sign into OneDrive, wait for the sync, and I am back. No “did I lose anything” anxiety. The canonical workspace was never on the dead laptop.

## Why OneDrive specifically

I use OneDrive because my employer gives me a generous quota and it is integrated into everything Microsoft I touch. That said, the pattern works with any cloud-sync provider: Dropbox, iCloud Drive, Google Drive, even self-hosted Syncthing. The requirement is “the same folder is on every device, and it stays in sync without me thinking about it”.

If you do not have a strong reason for one provider, pick the one that already syncs on the devices you use most.

## Two practical settings I would not skip

**Files On-Demand.** OneDrive can either download every file locally, or download files on demand when you open them. Files On-Demand is the right answer for a workspace folder. It means the folder shows up complete on every device, but only the files you actually open take up disk space. On a phone with limited storage, this is the difference between “the workspace is here” and “the workspace doesn’t fit”.

**Status indicators visible.** The little cloud icons next to each file tell you whether the file is fully synced, downloaded, or only available online. Get familiar with them. The two-second glance prevents the “I edited a stale copy” problem.

## The mobile Copilot app

This is the bit I get asked about most. The GitHub Copilot mobile app on iOS and Android can read files from cloud-synced folders that the OS exposes to it. On iOS, that means OneDrive files appear in the system file picker, and Copilot can read them. Same story on Android.

So when I am away from a laptop and want to brief myself before a meeting, I open Copilot mobile, point it at the customer profile, and ask for the brief. The same prompt I would use on my laptop. The same files. The same answer.

It is not as polished as the desktop experience. The mobile keyboard is the keyboard. But for “read these files and tell me X” it is enough.

## What I do not do

A few patterns I deliberately avoid:

- **I do not put secrets in the workspace.** API keys, passwords, tokens. The workspace is syncing to the cloud, multiple devices, and a private GitHub repo. None of that is appropriate for secrets. They live in a separate password manager. A `*.secret` line in `.gitignore` catches anything that slips in.
- **I do not put large binaries in the workspace.** Generated reports, video files, customer data dumps. Those go in a separate non-synced folder. The workspace stays small enough to sync fast, copy quickly, and remain navigable.
- **I do not put other people’s data in the workspace.** Customer-shared files stay in their own non-synced folder. The line between “things I produce” and “things others sent me” is worth maintaining.
- **I do not rely on a single device.** The whole point is that no device is special. If I caught myself thinking “I’ll do that on the work laptop later” the pattern would have failed. It hasn’t, because everything is everywhere.

## The thing that surprised me

The biggest shift was psychological, not technical.

When the workspace is on every device, I stop using “I’ll do that when I’m back at my desk” as an excuse. The desk is no longer a constraint. The customer note I want to add can be added now, from the seat at the airport gate. The diary entry I want to write can be written now, on the train. The briefing I want to read can be read now, in the back of a taxi.

The friction that used to push small tasks into “later” piles is mostly gone. Most of those small tasks just get done as I think of them. The pile is much shorter than it used to be.

## The mental model

A regular workspace is a thing that lives on a device. Devices are different. Some devices have the thing, some do not. You think about where the thing is.

A cloud-synced workspace is a thing that lives in the cloud and is cached on devices. All devices have the thing. You stop thinking about where it is, because the answer is everywhere.

This is a small mental change. It is also the change that makes most of the rest of the patterns in this series practical, because they all assume the same files are visible from wherever you happen to be working today.

---

That is the last post in the current batch. More will follow as the LinkedIn series produces new tips worth writing up.

Back to [[vscode-for-everything-that-isnt-code|the series intro]] for the full index, or jump to [[getting-started-vscode-copilot-personal-assistant|the getting-started guide]] if you are setting up from scratch.

The LinkedIn version of this tip went out on 26 May 2026.

0
0
votes

Article Rating
