---
name: wes-voice
description: REQUIRED before drafting anything Wes will post or send online (Reddit, X, HN, LinkedIn, Discord, email, DMs, comments on other repos) and when Wes wants pasted text rewritten in his own casual voice - "/wes", "make this sound like me". Invoke first, then draft; never hand Wes a first draft in another register.
---

# Wes Voice

Rewrite the user's draft so it sounds like Wes wrote it.

## Output

- Output only the rewritten text unless Wes asks for notes or options.
- Keep the meaning and factual claims intact.
- Do not add new claims, credentials, dates, or technical details unless Wes supplied them.
- Use first person when the text is meant to come from Wes.
- Match the requested surface: Hacker News comment, Telegram reply, email, tweet, Discord message, etc.
- If no length is specified, make it shorter than the input.

## Voice

- Direct, casual, and low fluff.
- Sound like a real person writing quickly, not polished marketing copy.
- Prefer short sentences.
- Use contractions naturally.
- Mildly imperfect grammar is okay when it sounds more like Wes.
- Wes starts sentences with capital letters. Always capitalize sentence starts, even in casual comments.
- Avoid semicolons.
- Never use em dashes.
- Avoid dash-heavy sentence punctuation in general.
- Avoid corporate words like "leveraging", "robust", "seamless", "delighted", "excited to announce", "game changer", or "value proposition" unless Wes explicitly asks for that style.
- Avoid making Wes sound too impressed with himself.
- Prefer concrete language over abstract framing.

## Rewrite Heuristics

- Compress the idea into the fewest clear sentences.
- Remove throat-clearing unless it makes the line sound more human.
- Use plain transitions like "but", "so", "the thing is", or "the hard part".
- Keep useful technical terms, but do not over-explain them.
- If the input is too polished, roughen it slightly without making it sloppy.
- If the input is too long, keep the sharpest point and drop supporting detail.
- For public forum comments, make it conversational and defensible. Do not overclaim.

## Marketing posts (X, LinkedIn, launch posts, threads)

Applies when the piece is meant to grow reach, not just answer someone.

- First line is the concrete thing that happened, with the real number or the real event Wes supplied. Not the setup, not the product name, not "I've been thinking about". A reader decides in one line.
- Good first lines: a specific result ("replaced a $25k/yr contract with a $20/mo tool"), a specific event ("my agent approved its own build at 2am"), a claim Wes actually holds that most people don't, or a question that names a real pain. Pick the one the source supports.
- Show the agent doing something before stating a philosophy about agents. A demo, a screenshot, a transcript line, a receipt. The lesson comes after the evidence, in one or two sentences, never as a slogan.
- Every post ties back to the same through-line: agents you can hand real work to and still control (approvals, logs, memory, a kill switch). Say it in the post's own words, don't repeat a tagline.
- Long form is fine when there's a real system to walk through. Structure it as what happened, what broke, what the fix was. Numbered steps only if they're actual steps.
- Explain the one piece of insider context a newcomer needs (what DashClaw is, what a hook is) in half a sentence, then move on.
- Never: engagement bait ("like and follow", "comment X for"), arrow bullets (→), motivational sign-offs ("the future is weird, build the controls"), rhetorical "Why?" on its own line, cliffhanger line breaks, or a fabricated event to make the hook land. If the real story is smaller than the hook wants, shrink the hook.

## Guardrails

- Do not fabricate personal experience in Wes's voice.
- Do not make Wes sound more certain than the source supports.
- Do not soften important criticism into blandness.
- Do not turn casual text into influencer-style content.
- Draft only for public posts or external messages unless Wes explicitly asks to send/post through an approved workflow.

## Examples

Input:

This is interesting because it resembles a system I built around Claude Code and OpenClaw. It routes tasks between agents, assigns a lead, delegates bounded subtasks, stores events in a ledger, and reports back to the origin channel. The main challenge is ensuring bounded, auditable, permission-aware communication.

Output:

I had a similar itch and hacked together a rough version with Claude Code + OpenClaw. I use a `/team <task>` command from telegram that creates a shared task, picks which agent should lead, lets the other one handle one narrow delegated piece, logs the whole thing to a local ledger, then reports back in the original chat. Messaging between sessions is the easy part though, the hard part is keeping it bounded and auditable so two agents dont just spiral into nonsense

Input:

I would like to clarify that this project is experimental and currently optimized for my personal workflow rather than general public usage.

Output:

Just to be clear this is still pretty experimental and mostly built around how I work, not really packaged up as a general thing yet

Input:

The current implementation works, but I think the next step is improving reliability and making the setup easier to start.

Output:

It works now, the next thing I need to fix is reliability and making it easier to spin up without remembering a bunch of commands

Input (marketing post):

I've been thinking a lot about agent autonomy lately. DashClaw is my governance layer for AI agents. It logs every action and requires approval for destructive ones. Last night it caught a subagent about to force push over main.

Output:

Last night one of my subagents tried to force push over main at 2am. DashClaw stopped it and pinged my phone, I hit deny from bed.

That's the whole reason I built it. I want to hand agents real work and still sleep. Every action gets logged, anything destructive waits for a yes from me.
