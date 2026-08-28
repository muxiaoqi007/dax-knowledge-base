---
title: "Teach AI to sound like you, not like AI"
url: "https://dax.tips/2026/06/01/teach-ai-to-sound-like-you"
published: "2026-06-01"
updated: "2026-06-01"
source: "dax.tips"
---

Earlier in the series I wrote about [[custom-instructions-teach-copilot-your-world|custom instructions]], the file that teaches Copilot who you are, what your workspace contains, and what you do.

That file handles the broad context. It does not handle voice. Voice is something else, deserves its own file, and benefits from being more concrete than a sentence or two of “I prefer plain English” can ever be.

What you actually want is a separate `voice-style.md` file the agent references whenever it is drafting prose on your behalf. Emails. Blog posts. Teams replies. LinkedIn comments. Anything that will leave your machine in your name.

## What goes in a voice file

Mine has four sections. Each one earns its place.

### 1. Hard rules

The non-negotiable list. Things that, if violated, mean the draft is unusable.

Mine starts with:

```
- No em-dashes (-). Ever. Use commas, periods, or restructure.
- NO EMOJIS. EVER. Not in emails, not in templates, not in option lists.
- No corporate / AI-ish phrasing. No "I'm excited to", "I'd love to",
  "Looking forward to", "Happy to", "Reaching out", "Circling back",
  "Per my last email", "Touching base".
- No bullet-list-everything. Prose first. Bullets only when genuinely lists.
- No "I just wanted to..." padding. Say the thing.
```

These are the things AI defaults to that I do not. Putting them at the top of the file, in shouty bold language, dramatically increases the chance the agent honours them. Voice files are not a polite request to the model. They are a contract.

### 2. Tone

A short paragraph capturing how you sound when you are at your best. Not aspirational. Actual.

Mine reads:

```powerquery
Direct, plain, pragmatic. Slightly dry. Mild humour OK if it lands.
Never forced. Not-overselling. Phil downplays his own work rather than
puffs it. Acknowledges others' contributions and ideas freely. Says
what he thinks, including when he disagrees, but never sharp or rude.
Comfortable saying "I don't know" or "let me look into it".

```

Notice this is half “what I do” and half “what I do not do”. Both halves matter. AI is much better at avoiding things when you tell it explicitly what to avoid than when you only describe what to aim for.

### 3. Sentence shape

Style rules at the sentence level. Length, voice, pacing.

Mine is short:

```
- Short sentences. Often one idea per sentence.
- Plain words over clever ones.
- Active voice.
- No buzzwords ("synergy", "leverage" as a verb, "drive alignment").
```

Four lines, but they catch most of the things that make AI prose feel artificial. Long sentences with three subordinate clauses. Passive constructions that hide the actor. Reaching for the impressive word when the plain word is right there.

### 4. Real examples

This is the section that does the most work, and the one most people skip.

A handful of short messages in the shape I actually write. Made up for this post, but the cadence, the brevity, the small typos are all real. Verbatim is the point.

Mine includes things like:

> “Sounds good. Lets do that next week”  
>   
> “No, don’t cancel. If you are happy to stay, I’d rather you did so we’re all in the same hotel”  
>   
> “Quite a few are asking. Its evolving nicely. I’ll send a writeup once I have the numbers”  
>   
> “Oh that one. Yeah, leave it for now”

AI is much better at imitating than at following abstract style rules. The examples teach by exhibit. They show the model what casual-but-still-mine looks like. They include the double-spaces after full stops, the occasional missing apostrophes (“Its”, “Lets”), the comfort with brevity.

I tell the model explicitly in the file: do not sanitise the chat-tone quirks when mimicking chat. Email is slightly more polished but still relaxed.

The examples are what makes the file work.

## The bootstrap trick

Writing a voice file from a blank page is hard. You end up with aspirational nonsense. Here is the trick that gets you to a good first draft in ten minutes.

Take twenty of your recent messages from any of the places you write a lot. Teams, email sent items, LinkedIn comments. Doesn’t matter where, just make sure they are genuinely yours and not formal corporate output.

Paste them into a chat. Ask the model:

```
Below are twenty messages I have written. Read them and extract the
consistent stylistic patterns: hard rules I seem to follow, tone, sentence
shape, anything quirky or distinctive. Format the output as a "voice
style" file an AI could use to imitate me. Be specific about what I do
and what I don't do.
```

The output is your first draft. It will be more accurate than anything you would have written about yourself unaided, because you are too close to your own voice to see it clearly. The model is not.

Refine from there. Add the hard rules you know matter. Drop anything the model invented. Add examples. Save the file.

## Where the voice file lives

Two options, both work.

**Inside the workspace** at something like `!Voice/voice-style.md` so it is visible to every chat. You either reference it from your `copilot-instructions.md` (“when drafting prose for me, follow `!Voice/voice-style.md`“) or you let the model find it on its own when it has reason to.

**In Copilot user memory** as `phil-voice-style.md` (or whatever your equivalent is). This is where mine lives. Memory persists across workspaces and across chats, so it travels with me whether I am in the customer workspace, a presentation prep workspace, or a quick scratch folder.

The choice is mostly logistical. Workspace files are easier to edit and version. Memory entries are easier to apply universally. Pick the one that matches where you write.

## How to use it

Three patterns.

**Reference in custom instructions.** A single line in your `copilot-instructions.md`: *“When drafting written communications on my behalf, follow the voice rules in `voice-style.md`.”* The agent picks it up implicitly whenever it is doing comms work.

**Explicit invocation.** When you want to be sure: *“Draft a reply to this email. Use the voice rules in voice-style.md.”* Hard to misinterpret.

**Voice check as a step.** When you ask the agent to draft something important, ask it to also self-check the draft against the voice file before showing it to you. The model is much better at flagging em-dashes after the fact than at remembering to avoid them in the first place.

## What this does not solve

Two honest caveats.

The voice file cannot make AI sound exactly like you. It will sound like a close approximation that catches eighty percent of your tone and tells. The remaining twenty percent is the difference between AI doing the draft and you doing the final pass. Both are needed.

The voice file also cannot account for who you are writing to. The same person writes differently to their manager than to a junior colleague. The voice file captures YOU; it does not capture the recipient. That is a separate problem, and the next post in this series is about how to solve it.

## A point I want to be very clear about

This technique is not about removing the human from the loop. It is about removing the trivial editing work so the human can focus on the bit that matters.

The opinion in the message has to be yours. The judgement call about whether to send the message at all has to be yours. The decision about what tone is right for this specific moment with this specific recipient has to be yours. None of that is delegable.

What the voice file delegates is the cognitive load you should not be spending energy on. Catching the em-dashes. Reworking the sentence that starts “I’m excited to”. Stripping out the buoyancy that you would have stripped out anyway. That work is real, it adds up over a day, and it is not the work you are paid for.

A useful test: would you have written something with this point and this tone if you had had the time? If yes, then having AI produce a draft that you then sharpen is a clean trade. If no, you are using AI to generate content you do not actually believe in, and no voice file will save the recipient from noticing.

How much proofing each draft needs is a judgement call too. A casual Teams reply to a colleague might need ten seconds of scanning. A note to your skip-level needs a careful read. A response to a customer escalation needs you to write it yourself, or at least to rebuild it from the AI draft until it is genuinely yours. The voice file lifts the load; it does not remove the responsibility for the call.

The point is to spend your attention where it matters. On whether the message lands. Not on whether the em-dashes were avoided.

## The mental model

A generic AI draft is a stranger writing in your name. Often competent. Always recognisably not you.

An AI draft against a voice file is the same stranger, but one who has read enough of your past writing to do a passable imitation. Not perfect. Close enough that the recipient does not pause and wonder.

That gap, between “this is recognisably AI” and “this passes as Phil”, is the difference between a tool you cannot rely on for comms and one you can.

## Practical tips

- **Update the file when you notice yourself fixing the same thing twice.** If you keep stripping out a phrase the agent inserts, add it to the banned list.
- **Be specific about banned phrases.** “Avoid corporate jargon” is too vague. “Never use ‘leverage’ as a verb. Never use ‘circle back'” is enforceable.
- **Keep the examples fresh.** Swap in newer ones every few months as your own voice drifts.
- **Do not sanitise yourself out of the examples.** If you write “Its” instead of “It’s” sometimes, leave it in. The model needs to see the real you, not a cleaned-up version.
- **Pair with a voice-check step in important drafts.** “Now read this draft back to me against the voice file and flag any violations.” Catches the slips.

---

Next in the series: persona files. The companion to voice files. If voice tells AI how YOU sound, personas tell it how to write to a specific RECIPIENT. The idea came from a chat with a colleague who pointed out the gap. Credit where due in the next post.

Back to [[vscode-for-everything-that-isnt-code|the series intro]] for the full index.

3.3
3
votes

Article Rating
