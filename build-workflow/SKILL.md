---
name: build-workflow
description: "Build an OmniDimension automation workflow from a plain-language goal: pick the trigger and nodes, wire them, validate, test with consent, then publish. Use when the user wants something to happen automatically after a call or another event."
---

Needs the OmniDimension MCP server: hosted at `https://mcp.omnidim.io/mcp`, or local with `npx -y @omnidim-ai/mcp-server` (see the connect-omnidim skill). If a field, error or feature is unclear, call `searchDocs` before guessing.

## Build an automation workflow from a plain-language goal: pick the trigger and nodes, wire them, validate, test with the user's consent, then publish.

Build an OmniDimension workflow for this goal:

"what should happen and when"

Read the `references/workflows.md` resource first. It explains triggers, outputs, ops and errors.

1. Pick the trigger, then find each node type with `listWorkflowNodeTypes` and read its fields with
   `getWorkflowNodeType`.
2. For every field that takes an id (a number, template, integration or agent), list real choices with
   `listWorkflowFieldOptions` and confirm them with the user. Never guess an id.
3. `createWorkflow`, then build it with `editWorkflow` batches. Use readable node ids, wire branches with
   `after` and `handle`, and add a notes node that says what the workflow is for.
4. `validateWorkflow` and fix every error.
5. A test run happens for real: messages, calls and CRM writes go out. Only call `testWorkflow` after the
   user says yes, with their own phone number or email in the payload. It returns at once, so poll
   `getWorkflowExecution` until `state` is `completed`, `failed` or `cancelled`.
6. Show the user the finished flow, then `publishWorkflow` when they approve. Publishing makes it live.
7. Prefer creating a new workflow. Never publish, activate, deactivate, delete or test an existing
   workflow without the user's explicit yes, and read it with `getWorkflow` first.


## References

- [workflows](references/workflows.md)
