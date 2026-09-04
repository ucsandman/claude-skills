---
name: harness-health
description: Read-only health check of the Claude Code harness - hooks, MCP servers, plugins. Use after editing settings.json/.mcp.json or hooks misbehave.
---

# Harness Health

Run: `pwsh -NoProfile -File "$env:USERPROFILE\.claude\scripts\harness-health.ps1"`

Read-only — the only file it ever writes is its own `harness-health.state.json` (next to the script). It scans every settings layer Claude Code merges — `~/.claude/settings.json`, `~/.claude/settings.local.json`, and each known project's `.claude/settings.json` / `.claude/settings.local.json` (projects come from `~/.claude.json`) — so a `/config` write in one repo is caught globally. It also reads the MCP config: the per-project entries in `~/.claude.json` plus every `.mcp.json` at a known project root and `~/.claude/.mcp.json`.

Switches:

- `-Probe` — additionally executes every distinct hook command once with `{}` on stdin, a 10s cap, and `CLAUDE_PROJECT_DIR` set to that layer's project (`$CLAUDE_PROJECT_DIR`/`$HOME` in the command are expanded first, since the probe runs through `cmd.exe`). Default runs execute nothing.
- `-IfChanged` — exits silently unless some `settings*.json` layer is newer than the state file. For hook wiring.

Interpret the output:

- **OVERRIDES** — project/local layers that shadow or extend the global file (outputStyle, model, effortLevel, hooks, permissions, env, enabledPlugins, disableAllHooks). Informational; act only if a project is overriding something you meant to be global. Also lists projects that carry `allowedTools`/`mcpServers` while `hasTrustDialogAccepted=false`.
- **disableAllHooks=true** — a layer switches every hook (guards included) off for that scope. Fix unless deliberate.

- **MISSING hook target** — a hook in some layer points at a deleted/moved script (`$CLAUDE_PROJECT_DIR`/`$HOME` are expanded first; the message names the file). Fix the path or remove the entry (use the update-config skill for settings.json changes).
- **DUPLICATE hook** — the same script is registered under the same event in two layers that both apply (a user layer plus anything, or two layers of one project), so it runs twice on every matching tool call. Two different projects never co-fire and are not reported. Delete the redundant entry — usually the project copy of something already global.
- **PROBE FAIL** (`-Probe` only) — the hook exited non-zero for a reason that is not a deny. Exit 2 is a legitimate hook DENY and is counted, not reported; anything else (or a 10s timeout) means the hook is broken for every tool call it matches. The first stderr/stdout line is quoted.
- **MISSING MCP command** — an stdio MCP server whose `command` neither resolves on PATH nor exists as an absolute file. The server will fail to start; fix the path or drop the entry. `http`/`sse` servers are counted only — this script never touches the network.
- **ORPHANED hook script** — a file in `hooks\` that no settings entry references. Wire it up or archive it. (`run_hook.cjs`-style launchers referenced by other repos' settings are exempt — check before removing.)
- **ENABLED plugin with no cache dir** — plugin enabled in settings but not installed/cached; re-install or disable it.
- **Cruft counts** — prune when flagged; CLAUDE.md backups and security-warning state files older than 7 days are safe to delete.
- **NEW SINCE LAST RUN / RESOLVED** — diff against `harness-health.state.json`. The first run prints `Baseline recorded`. Read NEW first: those appeared since the last check, so whatever you changed most recently caused them. A run without `-Probe` carries the previous probe verdict forward rather than declaring every PROBE FAIL resolved.

If issues were found and the fix is obvious and reversible, apply it; otherwise report to the user.

## Wiring it up

Do not wire this by hand-editing `settings.json` from a session; use one of these.

**(a) Weekly scheduled task** — full run with probe, logged next to the script:

```powershell
$act = New-ScheduledTaskAction -Execute 'cmd.exe' -Argument '/c pwsh -NoProfile -File "%USERPROFILE%\.claude\scripts\harness-health.ps1" -Probe > "%USERPROFILE%\.claude\scripts\harness-health.log" 2>&1'
$trg = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Sunday -At 9am
$set = New-ScheduledTaskSettingsSet -StartWhenAvailable -ExecutionTimeLimit (New-TimeSpan -Minutes 10)
Register-ScheduledTask -TaskName 'ClaudeHarnessHealth' -Action $act -Trigger $trg -Settings $set -Description 'Weekly read-only Claude Code harness health check'
```

Read the verdict with `Get-Content "$env:USERPROFILE\.claude\scripts\harness-health.log"`. (The task consumes the diff, so an interactive run right after it will show `0 new`.)

**(b) SessionStart hook** — one line, and free on sessions where no settings file moved (`-IfChanged` exits before doing any work). Add to `~/.claude/settings.json`:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          { "type": "command", "command": "pwsh -NoProfile -File \"~/.claude/scripts/harness-health.ps1\" -IfChanged", "timeout": 30 }
        ]
      }
    ]
  }
}
```

Never put `-Probe` in a hook: the probe executes every registered hook command, including this one.

Context: dormant skills were archived to `~\.claude\skills-archive\` on 2026-06-11 (restore = move the folder back to `skills\`). The DashClaw hooks live in `C:\Projects\DashClaw\hooks\` by absolute path — if DashClaw moves, this check is what catches it.
