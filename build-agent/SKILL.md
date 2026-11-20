---
name: build-agent
description: "Create an OmniDimension voice agent end to end: configure it, give it a phone number, test it with a real call, and roll it back to an earlier version if a change broke it. Use when the user wants a new voice agent, a bot for calls, or to undo agent changes."
---

Needs the OmniDimension MCP server: hosted at `https://mcp.omnidim.io/mcp`, or local with `npx -y @omnidim-ai/mcp-server` (see the connect-omnidim skill). If a field, error or feature is unclear, call `searchDocs` before guessing.

## Create a working voice agent end to end: configure it, give it a number, and verify it can place a call and speak.

Provision a working OmniDimension voice agent for this purpose:

"what the user wants the agent to do"

Follow these steps in order. Write tools take flat top-level arguments (no `requestBody` wrapper).

1. Voice + models: Call `listVoices` and pick a premade voice; use its `name` as voice_id. Remember not every listed voice synthesizes, so verify audio on the test call. A good default model is gpt-4.1-mini.
2. Create the agent with `createAgent` (flat top-level fields):
   {
     "name": "<short name>",
     "welcome_message": "<first line the agent speaks>",
     "context_breakdown": [ { "title": "Purpose", "body": "<the agent's instructions, derived from the purpose above>" } ],
     "call_type": "Outgoing",
     "model": { "model": "gpt-4.1-mini", "temperature": 0.5 },
     "voice": { "provider": "cartesia", "voice_id": "<voice id>" },
     "transcriber": { "provider": "soniox" }
   }
   Capture the returned `id` as agent_id. (`status` is always "Completed"; it is not a build signal.)
3. Give it a number: `listPhoneNumbers` -> pick a number `id`. Attach it with
   `attachPhoneNumber` { phone_number_id, agent_id }. If no number exists,
   either buy one (`searchPhoneNumbers` { region, carrier } then
   `purchasePhoneNumber`, which charges the account, so confirm with the user
   first and never pick for them; `carrier` is required on both calls in every
   region, and carriers do not stock the same numbers) or import one you
   already own (importTwilioNumber / importExotelNumber / importSipTrunk).
4. Optional knowledge base: `uploadKnowledgeBaseFile` then `attachKnowledgeBaseFiles` { file_ids, agent_id }.
5. (No test_number given.) Tell the user the agent is configured and offer to place a verification call to a number they control. Until a call log shows a non-empty `call_conversation`, do not claim the agent works end to end.

Throughout: phone numbers are E.164 with a leading `+`. See the `references/routing.md` resource for the full gotcha list.

## Safely roll an agent back to an earlier saved version: find the right snapshot, preview exactly what restoring changes, then restore.

Roll agent the agent id from listAgents back to an earlier version.

Goal: "(describe which earlier state you want back)"

Follow these steps in order. Do NOT restore blindly, preview first.
1. `listAgentVersions` { agent_id: the agent id from listAgents }. Versions come newest first; each has a `version_number`, `kind` (manual / auto / system), who saved it, when, and a `change_summary` (what changed in that version). Use the names, timestamps, and change summaries to find the one that matches the goal above. If version history is off for this org these calls return 403 (feature_disabled); report that and stop.
2. Preview the effect: `diffAgentVersion` { agent_id: the agent id from listAgents, version_number: <chosen>, against: "current" }. This shows exactly what restoring that version WOULD change vs the agent's live config, grouped by area (Settings, Prompt, Transfer rules, etc.). If it reports no meaningful change, tell the user there is nothing to restore and stop. Show the user this preview and confirm before the next step.
3. Restore: `restoreAgentVersion` { agent_id: the agent id from listAgents, version_number: <chosen> }. This automatically saves the current state as a "Backup before restore" version first (so the restore is itself undoable), then writes the chosen config back. Check the response's `skipped` list: knowledge files or integrations that no longer exist are dropped and reported, not restored.
4. Confirm: `listAgentVersions` again (a new system "Backup before restore" entry should be at the top) and tell the user they can undo by restoring it.

See the `references/agent-versioning.md` resource for the full tool list and the diff `against` modes.

## References

- [agent-config](references/agent-config.md)
- [agent-versioning](references/agent-versioning.md)
- [carriers](references/carriers.md)
- [recommended-stack](references/recommended-stack.md)
- [routing](references/routing.md)
- [voices](references/voices.md)
