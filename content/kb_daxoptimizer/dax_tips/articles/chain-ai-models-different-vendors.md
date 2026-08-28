---
title: "Chain AI models from different vendors to catch blind spots"
url: "https://dax.tips/2026/05/25/chain-ai-models-different-vendors"
published: "2026-05-24"
updated: "2026-05-24"
source: "dax.tips"
---

![A glowing scroll of light unfurling against a dark background.](https://i0.wp.com/dax.tips/wp-content/uploads/2026/05/mrlovebucket_A_single_glowing_document_or_scroll_of_light_flo_53afb884-400c-4162-a16d-0b6ed65af9e7_0.png?resize=1000%2C560&ssl=1)

*Part of the [VS Code + GitHub Copilot as a Personal Assistant](https://dax.tips/?p=3543) series.*

There is a thing people sometimes do when they want a better answer from an AI: they ask the same question to two models and pick the one they like. It works sometimes. It is not the right way to use multiple models.

The right way is to chain them in series, with a clear job at each step.

## The pattern

Three steps, one shared file in the middle.

1. **Hypothesis.** Chat A on Model X drafts an answer, an outline, an analysis, an email. The output goes into a file in the workspace.
2. **Critique.** Chat B on a different model (different vendor where possible) reads that file and critiques it. Spots flaws. Suggests improvements. Adds the things the first model missed.
3. **Iterate.** Chat A reads the critique and revises. Or you copy the best ideas yourself. Or you spin up Chat C on a third model for a tie-breaker.

The shared file is the unit of work that moves between chats. Not the prompt. Not the answer in isolation. The whole working document.

## Why cross-vendor matters

Models from the same family have similar training corpora and similar reinforcement learning patterns. They tend to have the same blind spots. If you ask GPT-4 to critique GPT-4’s own output, it gives you the version of feedback that GPT-4 thinks GPT-4 wants to hear. Polite, structural, often missing the things that matter.

Switch to a model from a different lab and the critique shape changes. Different training data, different finetuning culture, different priorities. The second model notices things the first one was trained not to notice. And vice versa.

The interesting comparisons are not “which model is better”. They are “which model catches what the other one missed”.

## What this looks like in VS Code

GitHub Copilot in VS Code lets you switch models inside the same workspace. Chat A picks one. Chat B picks another. The model picker is in the chat toolbar.

A typical flow for me:

- Chat A (Model X): “Draft a customer briefing for the meeting tomorrow with X. Use the customer profile and recent diary entries.”
- Chat A writes a 600-word draft into `!Briefings/X-2026-05-27.md`.
- Chat B (Model Y): “Read `!Briefings/X-2026-05-27.md`. What is missing? What is wrong? What is unclear? Be blunt.”
- Chat B comes back with five things. Three are good. One I disagree with. One I had not thought of.
- Chat A: “Incorporate the second and fifth points from this critique.”

Total elapsed time: maybe ten minutes. Result: a meaningfully better briefing than either model on its own would have produced.

## Flavours of the same pattern

The chain does not have to be just two models. Some variants I find useful:

- **Critic of the critic.** Sometimes the critique itself is too aggressive or too gentle. A third model can review the critique before it goes back to the first model.
- **Specialist roles.** Chat A drafts. Chat B fact-checks. Chat C edits for voice. Each model gets a job that suits its strengths.
- **Subagent variant.** Inside a single chat you can spawn a read-only subagent that uses a different model to perform a specific task and return the answer. This works for smaller jobs and avoids the chat-switching overhead. Subagents get their own post later in the series.

## A note on cost and time

Multi-model chains take more time and more tokens than a single chat. That is fine for things that matter. It is overkill for things that do not.

I use a single model for daily routine work. I bring in a second model when I am writing something I will publish, briefing a customer I care about, or working on a decision that has consequences. The judgement of when to escalate is part of the skill.

## The mental model

One model writing on its own is like one journalist working alone. Often fine. Sometimes blinkered.

One model critiquing its own work is like the same journalist editing their own article. They will spot typos. They will not spot bad arguments. They literally cannot see what they cannot see.

A second model from a different background is like a different journalist with a different newsroom culture reading the same draft. They notice different things. The whole point of the exercise is to capture that delta.

## Practical tips

- **Use the shared file, not chat-to-chat copy-paste.** A file on disk is durable. The critique can be re-read. The original draft is still there. Chat copy-paste loses context.
- **Be explicit about the role.** Tell Chat B exactly what it is doing. “You are reviewing this for X. Be specific. Flag anything wrong.” Vague critique requests get vague critiques.
- **Pick genuinely different models.** Two models from the same lab give you less value than one good cross-vendor pair. The point is to get different blind spots, not just different wording.
- **Do not always iterate.** Sometimes the critique surfaces that the original draft was on the wrong track entirely. Worth knowing before you polish it.

---

Next in the series: [Teach Copilot your world with custom instructions](https://dax.tips/?p=3546).

The LinkedIn version of this tip went out on 15 May 2026.

0
0
votes

Article Rating
