---
name: dispatch-blocks
description: "Use the established brief blocks when dispatching subagents or workflows."
---

# Dispatch Blocks

Reusable brief blocks for subagent dispatches. Each exists because its absence caused
a recorded failure (session-archaeology corpus, 2026-08-14; recurrence counts cited).
Pick EVERY block that matches the dispatch and paste it into the brief. Do not
paraphrase — these exact framings are the ones measured to work.

## Selection table

| Dispatch involves... | Blocks to include |
|---|---|
| 2+ agents editing one tree | OWNERSHIP |
| 3+ parallel agents on one repo/facts | GROUND TRUTH |
| Any bugfix task | REVERT-TO-RED |
| Feature wrapping an external CLI/binary | REAL-BINARY |
| Any gate/verify/test-running agent | RTK-DISTRUST |
| Proof/test-only task | PURE-TEST STOP |
| Tournament or design judging | DEFAULT-FATAL JUDGE |
| Review personas | READ-ONLY REVIEWER |
| New tool in ~/.claude/tools | HARNESS TOOL FAMILY |
| Launches or waits on a server/process | READINESS-POLL |
| Any Workflow agent() call | WORKFLOW-LEAN |
| Reads an API, MCP server, GitHub, docs, a web page or a window | DECLICK-FIRST |
| Builds anything that calls a model | NO-API |

Also: per CLAUDE.md, every dispatch sets `model:` explicitly, and test/build commands
run foreground with a timeout (slow-command-guard enforces this mechanically).

## DECLICK-FIRST — mandatory when the agent reads anything outside the repo
Lean agent types carry no MCP tools and no browser; declick is how they reach the same
targets, as trimmed JSON. Added 2026-09-03 (Wes: use declick wherever it saves tokens).

```
DECLICK FIRST. You have no MCP tools and no browser; you have the declick CLI.
Before WebFetch, curl, a screenshot or a DOM read: `declick list` (what exists),
`declick describe <name> --verb <verb>` (under 500 tokens), then
`declick run <name> <verb> <args> --fields a,b --limit N` (trimmed JSON, exit 0 ok).
GitHub = ghcli or github, docs = c7, X = xapi, DashClaw = dashclaw-mcp, Offlocal = offlocal.
A page's links/buttons/inputs = `declick web tree <url> --selector <css> --limit 20`; "does the page say X" = `declick web text <url> --grep X`;
a window = `declick desk tree <title> --interactive`.
Never edit ~/.declick by hand. Report the exact declick command you ran with each fact.
```

## WORKFLOW-LEAN — mandatory for every Workflow agent() call
Measured 2026-09-02 over 1,873 workflow spawns: the default agentType paid 51k tokens
of catalog before its first tool call; a lean type paid 27k. Not an instruction to
paste into a brief; a rule for the script itself.

```
Pair agentType with model on every agent() call:
  {model: 'sonnet', agentType: 'sonnet-implementer'}
  {model: 'opus',   agentType: 'opus-owner'}
  {model: 'haiku',  agentType: 'haiku-scout'}
Omit agentType only when the stage needs WebFetch, an mcp__ tool, Skill or Artifact,
and say why in the label. Never pair a lean type with a different model tier.
Fable synthesizer calls stay bare (no lean Fable type exists).
```

## OWNERSHIP — mandatory for 2+ agents in one tree
Reused verbatim ×21 across DashClaw, pineapple, phone-claude; its absence caused two
reflog-recovery incidents from shared-index races.

```
FILE OWNERSHIP: Edit ONLY the files on your list:
  <explicit file list>
Touch nothing else. Do not run ANY git commands (add/commit/stash/checkout) — the
coordinator owns staging and commits. Match the existing style of each file exactly.
Your test command is: <command>. Run it foreground with a timeout and read the real
exit code.
```

## GROUND TRUTH — mandatory for 3+ parallel agents on one repo
Reused ×11 in one sweep; its absence cost ~12 judge agents re-reading the same five
files from scratch.

```
VERIFIED GROUND TRUTH about <target> as of <date> (do NOT contradict these; they were
measured today):
  <bullet list of measured facts>
Do not re-verify these. Verify only what your specific task adds on top of them.
```

## REVERT-TO-RED — mandatory for every bugfix task
When mandated, 13/13 parallel fix agents complied and produced non-vacuous fixes; when
absent, a regression test shipped that still passed with its fix reverted.

```
Regression-test discipline (mandatory, in this order):
1. Write the regression test FIRST.
2. Prove it FAILS against the pre-fix code, with the failure message you expect.
   Get the pre-fix code from a COPY: `git show HEAD:<path> > <scratchpad>/<name>` and
   point the test at the copy, or copy the tree to the scratchpad. NEVER git stash,
   git checkout, git restore or git reset in the working tree: other agents are
   editing it, and a stash whose pop conflicts leaves everyone on a reverted base.
3. Apply the fix. Prove the test passes.
4. Report BOTH observations (the red run and the green run) in your DONE report.
A test never seen red proves nothing.
```

Step 2's mechanism is not optional: on 2026-09-03 (declick, 17-agent fix-findings run) a
reviewer stashed the shared tree to see the red run, its pop conflicted on a sibling's edit,
and a 25-file fix pass sat reverted under six concurrent agents for an hour. The
`git-tree-guard` hook now denies stash/checkout/restore/reset/clean in Bash as a backstop;
the block above is what keeps agents from needing it.

## REAL-BINARY — mandatory when the feature shells out to an external CLI
A 9-task plan shipped `dashclaw install openclaw` with 35 passing tests and the
feature had never run once — every test mocked the subprocess.

```
At least one verification step MUST execute the real <binary> end to end and assert on
its actual output. Mocked subprocess calls do not count toward "works". If the real
binary cannot run in this environment, report BLOCKED — do not substitute a mock and
call it verified.
```

## RTK-DISTRUST — mandatory for every gate/verify agent
Hand-injected ×9 into verification prompts; rtk compression misreported commit file
counts and hid a real pytest failure.

```
Do NOT trust compressed or piped summaries — pipe each command's output to a log file,
read the actual exit code separately, and quote failing lines from the log. If a
summary and an exit code disagree, the exit code wins; re-run with `rtk proxy` to see
raw output.
```

## PURE-TEST STOP — for proof/test-only tasks
Keeps bug-fixing and test-writing in separate reviewable commits.

```
This is a PURE TEST task — no production code changes of any kind. If a test failure
reveals a real production bug, STOP and report BLOCKED with the evidence (failing
test, observed vs expected) instead of patching production code inside this task.
```

## DEFAULT-FATAL JUDGE — for tournament/design judging
Reused verbatim ×5 per panel; skeptics repeatedly caught finders who checked one
instance of a pattern and concluded about all of them.

```
You are an adversarial judge. Your DEFAULT VERDICT IS FATAL — assume the proposal is
broken until it survives your attack. Attack the weakest load-bearing claim first.
State explicitly how many instances of each pattern you examined (not just the first).
A verdict of "survives" requires naming what you tried that failed to kill it.
```

## READ-ONLY REVIEWER — for review personas
Reused verbatim ×5 across a 5-persona pre-merge audit.

```
Do not edit files. Produce concise findings with file paths and line numbers,
severity, and rationale. Also say explicitly if there are no material findings.
```

## HARNESS TOOL FAMILY — for new tools in ~/.claude/tools
Produced five clean single-pass tool builds back to back in one afternoon.

```
This machine has a family of zero-dependency Node tools in
~/.claude/tools/ (spend, recall, gitradar, cronwatch, envdoctor). Match
their conventions exactly: single .cjs file, zero npm dependencies, generated .html
output is gitignored, a README.md, and an --open flag that opens the rendered page.
```

## READINESS-POLL - mandatory for any dispatch that starts or waits on a server/process
A subagent sat 20 minutes "waiting for the ready signal" on 2026-08-21 while both
ports were already LISTENING; launch.py had been up in 30s. Notifications are not
truth; the port is.

```
Do not wait for a ready notification. Poll the real condition (port listening /
health URL 200 / the exact log line) in a tight loop with a HARD cap (launch.py
90s, local web app 60s, API 30s). When it passes, proceed immediately. On timeout,
read the log and report BLOCKED with the exact blocker - never extend the wait.
Finish the whole task within <N> minutes or report BLOCKED.
```

## ONE-SHOT FOREGROUND — mandatory for every detached claude -p builder
The 2026-09-04 miplib-open builder started a 27 minute HiGHS screen with
`run_in_background`, replied "I'll continue when it completes", and exited at 17 minutes
with 6 of 40 instances measured and nothing committed. A `-p` run is one turn; ending the
turn kills the process and every child. `detached-builder.mjs` prepends this automatically
and flags a promise-shaped result as ABANDONED; put it in any brief that goes another way.

```
You are a one-shot claude -p run. The moment you end your turn the process exits and every
background task, shell job, or child you started dies with it. NEVER use Bash
run_in_background, Monitor, or "I will continue when it completes". Run long measurements in
the foreground with an explicit timeout, split into chunks under 10 minutes each, read each
chunk's output, and keep working until the whole brief is finished and pushed.
```

## NO-API — mandatory for every builder that writes code calling a model
The 2026-09-03 TradesDesk builder wired `@anthropic-ai/sdk` with an `ANTHROPIC_API_KEY`
because its brief never said not to. Wes's rule: subscriptions only, never per-token API.

```
Model calls in anything you build run through the Claude Code CLI (claude -p, argv array,
stdin prompt, --output-format json, env stripped of every CLAUDE*/ANTHROPIC_* var) or the
Codex CLI on the monthly subscription. NEVER the Anthropic API, never @anthropic-ai/sdk or
api.anthropic.com, never an ANTHROPIC_API_KEY in code, env examples, or docs. Reference:
C:\Projects\discovery-loop\loop.py call_model.
```
