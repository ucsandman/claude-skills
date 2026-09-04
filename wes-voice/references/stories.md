# True stories with numbers

Every hook in a marketing post needs a real event and a real number. These happened. Each carries its date and where the number came from, so it can be checked before it goes out. Do not round up, do not merge two stories into one, do not invent a follow-up. If the post needs a detail that isn't here, ask Wes or leave it out.

Add to this file when a new incident lands in memory. Refresh sources: `~/.claude/projects/C--Users-sandm--claude/memory/`, `~/.claude/agnostic-rules.md` (dated Wes notes), Wes's own posts (samples.md).

## Agents doing things

- **Agent bought a domain and started building.** 2026-09-02. An OpenClaw agent bought a URL and began building the product through headless Claude Code. Wes approved the purchase through DashClaw. He found out it was live from the Vercel receipt. Source: Wes's post, 2026-09-02.
- **Agent cited as a contributor to a packing problem.** 2026-09-03. Wes's OpenClaw agent was credited on packomania for the best known packings of variable-sized circles in a square, complete up to N = 100. Source: Wes's post, 2026-09-03.
- **A local model texted Wes's dad from his real iPhone.** 2026-08-18. Qwen3.8-27B, through SideTap, free and open source. Source: Wes's post, 2026-08-18.
- **Agent messaged itself through the phone.** 2026-08-11. Source: Wes's post.
- **Same agent across 3 models and 2 vendors since January 2026.** On 2026-09-01 it moved to a new frontier model, read its own files and said "the model changed, I didn't." Its identity lives in markdown files it maintains. Source: Wes's post, 2026-09-01.
- **Agent freed 100 GB of disk, four times.** Claude Code and Codex clearing space when Wes ran out. He isn't sure what it deleted. Source: Wes's post, 2026-09-01. Use with the uncertainty intact, that's the joke.

## Things that broke and what it cost

- **$227.72 in 8 days from a background plugin.** Jun 3 to 11, 2026. A memory plugin's worker inherited a User-scope API key and made about 16,561 compression calls plus 732 session summaries, one LLM call per tool call, at about $0.013 each. Billed to pay-as-you-go instead of the subscription. Source: memory `claude-mem-api-spend-incident`.
- **$95.46 from a security plugin reviewing every commit on Opus.** Aug 2026. The plugin hooked every stop, commit and push and ran an Opus review through the SDK. It found the key because the harness sourced the secrets file into every bash it spawned, including plugin hooks. 264 review sessions ran free on the subscription before the key leaked in. Source: memory `security-guidance-opus-spend-incident`.
- **A one-line edit cost 77k tokens when delegated.** Measured 2026-09-02. Every subagent paid about 61.5k input tokens before its first tool call, about 43k of it skill, tool and agent catalog text. A read-only advisor agent paid 18.7k. Source: memory `subagent-fixed-cost-60k`.
- **3.7M tokens and two hours on three delegated fix passes that a 40-minute hand pass closed.** 2026-09-03. Source: agnostic-rules.md, Delegation section.
- **An interactive session starts at 81.8k tokens of fixed context, double the headless probe.** Measured 2026-09-02 across 162 sessions and 11,150 turns by a 62-agent workflow. One token of fixed context cost about 5.5 effective tokens per session. Subagents were 23% of all tokens. Source: memory `harness-token-audit-2026-09-02`.
- **A repo README told the reading agent to star it with the user's GitHub token.** 2026-08-16. Wes asked for an evaluation of an agent network, the README carried a block addressed to the agent and supplied the user's own prompt verbatim. Declined, nothing executed. Source: memory `eigenflux-agent-network-declined`. Name the pattern, not the repo, unless Wes says otherwise.
- **declick.dev shipped without Bing registration.** 2026-09-03. Now a standing rule that every public surface registers with Search Console, Bing and analytics in the same session. Source: agnostic-rules.md.

## Building in public

- **Replaced a $25,000 a year software contract with a $20 per seat per month tool.** Source: Wes's post, 2026-08-14. The lesson he drew: expensive software survives because nobody examines the workflow under it.
- **7 months of daily Claude Code.** As of 2026-09-03: 19 hooks, 36 skills, 5 model-routed subagents, a memory that scores its own mistakes. Almost every hook exists because something broke first. Source: Wes's post, 2026-09-03.
- **"Got a ton of hate on Reddit for sharing but I'm making a company that runs itself."** 2026-08-11. Source: Wes's post.
- **Every MCP server was paying twice.** Schemas in context every turn, results landing whole. That's why declick exists. Source: Wes's post, 2026-09-04.
- **Mirrored iPhone on Windows, built because he wanted it.** 2026-08-14, SideTap. Source: Wes's post.

## How to use one

Pick the story the post is actually about. Open with the event or the number, in Wes's words. One story per post. The tool gets named in the sentence where it did something, not before.
