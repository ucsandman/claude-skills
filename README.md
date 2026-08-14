# claude-skills

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
![Skills](https://img.shields.io/badge/skills-26-brightgreen)
![Platform](https://img.shields.io/badge/platform-Claude%20Code-orange)

A working set of 26 [Claude Code](https://claude.com/claude-code) skills, used daily
on real projects. Code review, shipping, marketing assets, device control, and agent
orchestration.

These are not demos. Every one of them started as a task that went wrong, or went
slowly, enough times to be worth writing down.

## What a skill is

A skill is a folder with a `SKILL.md` inside it. The file carries YAML frontmatter
with a `name` and a `description`, then plain Markdown instructions.

```markdown
---
name: blindspot
description: Use before modifying unfamiliar or high-stakes code...
---

# Blindspot
...instructions Claude follows...
```

Claude Code reads the `description` of every installed skill at session start, and
loads the full body only when the description matches what you are doing. So a skill
costs almost nothing until it fires. You can also invoke one by hand as `/blindspot`.

## Install

Skills live in `~/.claude/skills/`. Take all of them:

```bash
git clone https://github.com/ucsandman/claude-skills.git ~/.claude/skills
```

Or take one:

```bash
git clone https://github.com/ucsandman/claude-skills.git /tmp/claude-skills
cp -r /tmp/claude-skills/blindspot ~/.claude/skills/
```

Start a new Claude Code session and the skills are live. Run `/blindspot` to check.

To scope a skill to one project instead of your whole machine, put it in
`.claude/skills/` inside that repo.

## The skills

### Thinking and review

| Skill | What it does |
|---|---|
| `interview` | Interrogates a vague request before any code is written. For "make it better" and "add auth". |
| `blindspot` | Surfaces what you are missing before you touch unfamiliar or high-stakes code. |
| `adversarial-review` | Parallel review agents find issues, then a skeptic that defaults to REFUTED tries to kill each finding. Only survivors are reported. |
| `de-vibe` | Finds and fixes the tells that software was AI-generated, then gives it a small distinctive identity. |
| `frontend-verify` | Drives the app after a UI change. Reads console and network errors first, writes full page state to disk, and only pulls a screenshot into context when a route actually fails. |

### Shipping

| Skill | What it does |
|---|---|
| `ship` | The end-of-work ritual. Docs, commit, push, and a security spot-check that blocks the ship like a failed test. |
| `wrap` | Session handoff. Writes durable notes and a resume prompt before a context or rate limit hits. |
| `harness-health` | Read-only check of the local Claude Code setup. Finds dead hook paths, orphaned hook scripts, and enabled plugins with no cache. |
| `fable-gpt` | Hands heavy implementation and debugging to Codex, and orchestrates Claude plus Codex in one session. |

### Marketing and launch

| Skill | What it does |
|---|---|
| `marketing-studio` | Shared background for every asset skill below. Remotion, Blender, and Playwright pipeline. |
| `logo-reveal` | Logo animation and brand intro video. |
| `social-clip` | Short feature-announcement clip for X, LinkedIn, or TikTok. |
| `product-demo` | Screen recording with zooms and a real cursor. |
| `launch-video` | 30 to 90 second announcement composing demo footage, logo, and copy. |
| `og-assets` | OG images, link previews, animated OG loops, and README GIFs. |
| `audio-track` | Generated music, voiceover, and narration. |
| `marketing` | Runs the whole asset suite for a product in one pass. |
| `launch` | Launch copywriting and strategy, including the virality and positioning references. |
| `ship-it` | Renders the assets, then posts the launch with videos attached. |

### Agents and orchestration

| Skill | What it does |
|---|---|
| `team` | Fans one task across two agents. Picks a lead, delegates bounded subtasks, logs every exchange to a ledger, and reports back. |
| `meditate` | A nightly reflection loop. Observations have to earn their way up a ladder from note, to memory, to standing rule. Most nights promote nothing, which is the point. |
| `dashclaw-governance` | Governance protocol for a supervised agent. Risk thresholds, approval waits, and action recording. |

### Devices

| Skill | What it does |
|---|---|
| `phone` | Drives a real iPhone over USB. Send and read messages, open apps, tap, scroll, and OCR the screen. |
| `phone-gotchas` | The coordinate traps and silent failures that iPhone automation hits. Required reading before the first tap. |
| `deskclaw` | A read-only view of the Windows desktop. Lists windows, dumps a window's accessibility tree, and takes screenshots. |

### Personal

| Skill | What it does |
|---|---|
| `wes-voice` | Rewrites a draft so it sounds like a person wrote it quickly, not like marketing copy. |

## Layout

```
claude-skills/
├── LICENSE
├── README.md
└── <skill-name>/
    ├── SKILL.md          # required: frontmatter + instructions
    ├── references/       # optional: loaded on demand, keeps SKILL.md small
    ├── scripts/          # optional: executable helpers
    └── evals/            # optional: test cases for the skill
```

The `references/` split matters. `SKILL.md` stays short so it is cheap to load, and
the long material sits in a reference file that Claude reads only when it needs it.

## Before you clone

A few of these skills call local tooling by path, such as a rendering pipeline or a
message ledger, and expect it to exist. Read the `SKILL.md` before running one and
adjust the paths for your machine. The reasoning in each skill is portable even where
the paths are not.

`phone` and `deskclaw` are Windows plus iPhone specific. Everything in Thinking and
review is plain Claude Code and runs anywhere.

## Third-party skills

Some skills installed alongside these are other people's work under their own
licenses. They are deliberately not redistributed here. Install them from source:

| Skill | Upstream |
|---|---|
| `claude-api` | [anthropics/skills](https://github.com/anthropics/skills) (Apache 2.0) |
| `six-hats`, `lateral`, `inversion` | [danium/lateral-thinking](https://github.com/danium/lateral-thinking) |
| `polish` | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) |
| `agent-browser` | [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) |

Everything tracked in this repository is original work.

## Contributing

Issues and pull requests are welcome, especially bug reports on a skill that misfires
or fails to trigger when it should. If you fork a skill and make it better, a link
back is appreciated but not required.

## License

MIT. See [LICENSE](LICENSE).

## Support

If my tools save you time, you can support my work here:

[![Sponsor on GitHub](https://img.shields.io/badge/GitHub%20Sponsors-%E2%9D%A4-db61a2?logo=githubsponsors&logoColor=white)](https://github.com/sponsors/ucsandman)
[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-%E2%98%95-ffdd00?logo=buymeacoffee&logoColor=black)](https://buymeacoffee.com/wes_sander)
