# Agent version history

A **version** is a frozen snapshot of an agent's configuration at a point in
time. Use it to save a known-good setup before a risky change, and to roll back
if a change makes the agent worse.

## The tools

- `listAgentVersions` { agent_id } - the timeline, newest first. Each entry has a
  `version_number`, a `kind`, who saved it, and a `change_summary` (a one-line
  "what changed in this version vs the one before it").
- `createAgentVersion` { agent_id, name, note? } - save the agent's CURRENT
  config as a named version.
- `diffAgentVersion` { agent_id, version_number, against? } - what changed (below).
- `restoreAgentVersion` { agent_id, version_number } - write a version back onto
  the live agent.
- `renameAgentVersion` / `deleteAgentVersion` - tidy the timeline.

`kind` is `manual` (a person saved it), `auto` (saved automatically a few minutes
after editing settles - you do NOT trigger these), or `system` (a safety backup
taken automatically right before a restore).

## When to snapshot

Call `createAgentVersion` right BEFORE a change you might want to undo (swapping
the model/voice, rewriting the prompt, changing transfer rules). Give it a clear
`name`. You do not need to snapshot after routine edits - the platform auto-saves
settled states on its own.

## How to read a diff

`diffAgentVersion` returns `{ changed, groups }`, each group an area (Settings,
Prompt, Transfer rules, Post-call actions, Knowledge, Integrations, Web widget,
Conversation flow, Other settings) with the specific old -> new changes. Pick the
comparison with `against`:

- **omit / `against=previous`** (default): what changed IN this version vs the
  one before it. Matches the `change_summary` in the list.
- **`against=current`**: what restoring this version WOULD change vs the agent's
  live config right now. Read this before a restore to preview the effect.
- **`against=<number>`**: compare with a specific other version.

## How to decide and perform a restore

1. `listAgentVersions` and scan `change_summary` to find the target version.
2. `diffAgentVersion` { version_number, against: "current" } to preview exactly
   what restoring changes. If nothing meaningful changed, there is nothing to do.
3. `restoreAgentVersion` { agent_id, version_number }. This first saves the
   current state as a `system` "Backup before restore" version (so the restore is
   itself undoable), then writes the chosen config back. The response's `skipped`
   list reports references (knowledge files, integrations) that no longer exist
   and were dropped rather than failing the restore.
4. Confirm with `listAgentVersions` (a new `system` backup appears at the top).

## Gotchas

- Version history is enabled per organization. If it is off for the caller's org,
  these endpoints return 403 (`feature_disabled`).
- `createAgentVersion` refuses a no-op: if nothing changed since the latest
  version it returns 409 (`no_changes`). Make a real change first.
- There is a per-agent cap on versions created through the API; at the cap
  `createAgentVersion` returns 409 (`version_limit_reached`) - delete an old one.
- `listAgentVersions` and `diffAgentVersion` are read-only and safe to call
  freely. Restore and delete mutate the live agent - confirm intent first.
