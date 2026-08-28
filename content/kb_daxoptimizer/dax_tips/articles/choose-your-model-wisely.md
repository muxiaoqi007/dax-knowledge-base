---
title: "Choose your model wisely: premium for design, cheap for execution"
url: "https://dax.tips/2026/06/08/choose-your-model-wisely"
published: "2026-06-08"
source: "dax.tips"
---

Almost every project, whether it is a software build or a knowledge-work effort like this blog series, has two distinct phases.

There is **design**. The hard thinking. Structure, architecture, the order things should go in, the tricky judgement calls, the “what are we actually trying to do here” conversations. This is where a premium reasoning model earns its keep. The difference between a top-tier model and a mid-tier one is real and visible when the problem is genuinely hard.

There is **execution**. Once the design is locked, most of what follows is mechanical. Drafting the thing the plan describes. Filling in the structure. Producing the next item in a pattern that has already been established. This work does not need the best model. A faster, cheaper model does it just as well, because the hard thinking already happened in the design phase.

Sending a premium model to do execution work is like sending the senior partner to photocopy the brief. They can do it. It is a waste of them.

## A lived example: this blog series

This series is the proof.

The first post took a lot of back and forth. What is the framing. What order should the posts go in. What is the title. How opinionated should the voice be. Where do the cross-links go. That is design work, and I used a premium reasoning model for it, because getting the structure right at the start pays off across every post that follows.

By the eighth post, the shape was locked. Each new post followed an established pattern: hook, premise, the mechanic, why it matters, practical tips, the mental model, cross-links. Producing one was much more mechanical. That is execution work. A cheaper, faster model drafts it perfectly well, because the thinking that makes the posts good already happened when the series was designed.

Same project. Two phases. Two different right answers for which model to use.

## The cost angle

There is a cost lens on all of this, and it is worth being explicit about.

The cheapest token is the one you never spend.

Premium models cost more per token and are slower to respond. That is fine when the reasoning earns it. It is waste when it does not. A rough hierarchy for any task:

1. **Does this need a model at all?** A surprising amount of what people send to an LLM is deterministic work a script does instantly and for free. Renaming two hundred files. Reformatting a spreadsheet. Extracting dates from filenames. Git operations. You do not need a probabilistic reasoning engine for `Get-ChildItem | Rename-Item`. You need five lines of PowerShell. More on this in the next post.

1. **If it needs a model, does it need a premium one?** Reserve the expensive model for genuine reasoning: design, architecture, hard judgement, ambiguous problems. This is where the quality difference is real.

1. **For everything else, use the cheap model.** Execution, drafting, summarising, formatting that is slightly too fuzzy for a script. The cheap model is fast, good enough, and a fraction of the cost.

Most people collapse all three into “use the best model for everything”. They pay for premium reasoning on tasks that needed none.

## How to switch in practice

In VS Code, GitHub Copilot lets you change models from the model picker in the chat toolbar. The switch is one click and it persists per chat.

My actual habit:

- **A design chat** runs on a premium model. This is where I work out the structure of something, argue through options, make the hard calls.
- **An execution chat** runs on a cheaper, faster model. Once the design chat has produced a plan, I either switch the model in place or open a fresh chat on the cheaper model and hand it the plan.
- **The plan is the handoff artifact.** The design model writes down what to do. The execution model does it. The plan lives in a file, so the handoff is clean and the execution model is not paying to re-derive the thinking.

This is the same hypothesis-then-execute split that runs through a lot of this series. The design model thinks. The execution model acts. The file in the middle carries the decision.

## When NOT to optimise

A caveat, because optimising model choice can itself become a waste of time.

For small one-off tasks, do not agonise. Just use whatever chat is open. The overhead of switching models and thinking about cost is not worth it for a thirty-second job. The model-matching discipline pays off on **sustained, repeated, or large** work, where the cost and time differences actually add up. A single email does not need a cost-benefit analysis. Drafting forty of them does.

The judgement of when the optimisation is worth it is itself part of the skill.

## The mental model

A premium model is the architect. Expensive, in demand, brilliant at the hard structural decisions, wasted on routine work.

A cheap model is the builder. Fast, capable, gets the actual thing made once the plans are drawn.

A good project uses both, in the right order, for the right parts. You would not pay an architect’s day rate to lay bricks. You also would not ask a bricklayer to design the building. The skill is knowing which phase you are in and picking accordingly.

## Practical tips

- **Do the design phase first, deliberately.** The whole pattern depends on the thinking being done before execution starts. If you blur the two, you end up paying premium rates for execution because you never locked the design.
- **Write the plan to a file.** The handoff between the design model and the execution model should be a durable artifact, not a chat message. It is cheaper, clearer, and re-runnable.
- **Default new chats to a sensible mid-tier model.** Reserve the premium model for chats you deliberately open for hard thinking. Do not let the expensive model be your default for trivia.
- **Notice when a task is actually deterministic.** If the answer is the same every time and follows fixed rules, it does not need a model at all. Write the script. (Next week’s tip.)
- **Do not over-optimise small tasks.** The discipline is for sustained and repeated work. A single quick job is not worth the switching overhead.

---

Next in the series: let scripts do what scripts do. The cheapest token is the one you never spend, and a surprising amount of what people hand to an LLM is deterministic work a script does instantly and for free.

Back to [[vscode-for-everything-that-isnt-code|the series intro]] for the full index.

0
0
votes

Article Rating
