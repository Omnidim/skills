---
name: omnidim-run-campaign
description: "Set up and launch an OmniDimension bulk-call campaign: draft it, add contacts in batches, size concurrency, check calling hours, then start and watch it. Use when the user wants to call a list of people."
---

Needs the OmniDimension MCP server: hosted at `https://mcp.omnidim.io/mcp`, or local with `npx -y @omnidim-ai/mcp-server` (see the omnidim-connect skill). If a field, error or feature is unclear, call `searchDocs` before guessing.

## Set up and launch a bulk-call campaign safely: draft it, batch the contacts in, size concurrency from the numbers, check calling hours, then start and watch it.

Set up and launch an OmniDimension bulk-call campaign for this goal:

"<who to call and why>"

Follow these steps in order. Write tools take flat top-level arguments (no `requestBody` wrapper).
A campaign dials real people and spends real money: confirm the plan with the user before
`startBulkCall`, and never start one they have not approved.

1. Decide the shape: a one-time list is a normal campaign; continuously arriving leads mean a
   dynamic campaign (`is_dynamic: true`) fed by `addBulkCallContact` instead of steps 3 and 5.
2. Number and agent: Call `listPhoneNumbers` and pick the dialing number WITH the user; never pick silently. If the number has an agent attached it runs the calls; otherwise pass a `bot_id`.
3. Create it as a draft with `createBulkCall`
   { name, phone_number_id, save_as_draft: true, concurrent_call_limit: <sized below> }.
   Add `rotation` when dialing from several numbers (see the bulk-campaigns resource for the
   shape). Keep the returned `id`.
4. Size concurrency from the goal's numbers, not a guess: per channel, 3600 / average call seconds
   gives calls per hour (~40 at 90s); divide the target hourly rate by that. Show the arithmetic
   to the user.
5. Add contacts with `addBulkCallContacts` in batches of up to 1000:
   { bulk_call_id, contacts: [{ to_number: "+1...", custom_variables: {...} }] }.
   This shape differs from create's contact_list on purpose; do not mix them. Report anything
   that comes back in `rejected` with its row index. Each `custom_variables` key fills the
   matching [placeholder] in the agent's greeting and prompt, so every row needs every key the
   agent uses, or the agent says the placeholder out loud.
6. Calling hours: if the goal names a time window, set it with `setBulkCallDailyTimeControl`
   (both the stop and start blocks are required) and pass `daily_stop_timezone`/
   `daily_start_timezone` for the customers' region; left out they fall back to the campaign's
   timezone, and a mismatch dials people at night. Also set the agent's own `timezone` field
   (`updateAgent`) so it works with the customers' local time on calls.
7. Check the agent's greeting against the `references/call-tips.md` resource (a name check of
   8 words or fewer loses far fewer calls in the first seconds). Suggest changes; never edit the
   agent without the user's yes.
8. Show the user the plan (campaign id, contact count, concurrency, window, rotation) and get
   their explicit go-ahead. Then `startBulkCall`.
9. Watch it: `getBulkCallLiveStatus` for progress. When it finishes, read per-contact outcomes
   with `listBulkCallLines` (follow `next_cursor` until null) and offer `retryBulkCall` for
   contacts that did not connect.

See the `references/bulk-campaigns.md` resource for the full rules and failure modes.

## References

- [bulk-campaigns](references/bulk-campaigns.md)
- [call-tips](references/call-tips.md)
- [carriers](references/carriers.md)
- [recommended-stack](references/recommended-stack.md)
- [voices](references/voices.md)
