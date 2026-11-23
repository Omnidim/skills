---
name: omnidim-audit-calls
description: "Review OmniDimension call logs: find failed calls, read transcripts and sentiment, and summarize an agent or a campaign. Use when the user asks how calls went or why calls fail."
---

Needs the OmniDimension MCP server: hosted at `https://mcp.omnidim.io/mcp`, or local with `npx -y @omnidim-ai/mcp-server` (see the omnidim-connect skill). If a field, error or feature is unclear, call `searchDocs` before guessing.

## Review and summarize call logs: find failures, inspect transcripts and sentiment, or audit a specific agent or campaign.

Audit OmniDimension call logs for this question:

"<what the user wants to know about their calls>"

1. List recent calls with `listCallLogs` { "pagesize": 5, "include_details": false }.
   - Keep `include_details: false` and `pagesize` 5 while you scan. Even short rows are about 2.5 KB, and the response is trimmed past a size cap: a page of 50 short rows comes back as 6. Narrow with filters and `pageno` instead of a bigger page.
   - Filters: `agentid` (note: no underscore) for one agent, `bulk_call_id` for one campaign, `call_status` for triage. The status enum uses a hyphen: completed | busy | failed | no-answer.
   - Each row carries `id`, `call_status`, `sentiment_score` and cost. There is no date-range filter; filter by `time_of_call` (MM/DD/YYYY HH:MM:SS) yourself if needed.
2. For each call of interest, call `getCallLog` { call_log_id: <row id> } for the full transcript (`call_conversation`), `interactions`, `extracted_variables`, `recording_url`, and per-turn latency/cost. Note: an empty `call_conversation` means the agent never spoke (a silent call), not that the call is missing.
3. Summarize the answer to "<what the user wants to know about their calls>": group by status or agent, surface failure reasons and low-sentiment calls, and cite specific `call_log_id`s.

See the `references/routing.md` resource for the full gotcha list.

## References

- [agent-config](references/agent-config.md)
- [carriers](references/carriers.md)
- [routing](references/routing.md)
