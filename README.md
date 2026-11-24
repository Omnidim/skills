# OmniDimension skills

Teach your AI agent to build OmniDimension voice agents, test them with a real call, run calling campaigns, automate what happens after each call, and review how the calls went.

## Install

**Claude Code** gets all six skills and the MCP server in one step:

```
/plugin install omnidim --marketplace Omnidim/skills
```

On Claude Code before version 2.1.275, run `/plugin marketplace add Omnidim/skills`, then `/plugin install omnidim@omnidim`.

**claude.ai and the Claude desktop app** (paid plans) get the same plugin:

1. Open **Customize**, then **Plugins**.
2. Click **Add**, then **Add marketplace**, and enter `Omnidim/skills`.
3. Install **OmniDimension**.
4. On the plugin's **Connectors** tab, connect OmniDimension and sign in.

**Codex, Cursor, Gemini CLI, Copilot, OpenCode** and [more](https://github.com/vercel-labs/skills#readme) get all six skills:

```
npx skills add Omnidim/skills --skill '*' -g -y
```

**Any other agent:** copy the folders in [`skills/`](skills) into its skills folder.

The skills need the OmniDimension MCP server. The Claude Code plugin adds it for you: run `/mcp` once to sign in. For any other agent, ask it to "connect to OmniDimension" and the `omnidim-connect` skill walks it through. The [MCP docs](https://docs.omnidim.io/docs/mcp) have the steps for each client.

## Skills

In the order you usually need them:

| Skill | Ask your agent | What it does |
|---|---|---|
| [omnidim-connect](skills/omnidim-connect/SKILL.md) | "Connect to my OmniDimension account" | Sets up the hosted or local MCP server, read-only if you want |
| [omnidim-build-agent](skills/omnidim-build-agent/SKILL.md) | "Build an agent that books dental appointments" | Configures the agent, gives it a number, tests it with a real call, rolls it back if needed |
| [omnidim-run-campaign](skills/omnidim-run-campaign/SKILL.md) | "Call these 500 leads about the webinar" | Drafts the campaign, adds contacts, checks calling hours and concurrency, then starts it |
| [omnidim-build-workflow](skills/omnidim-build-workflow/SKILL.md) | "After each call, send hot leads to Slack" | Builds, tests and publishes the automation |
| [omnidim-audit-calls](skills/omnidim-audit-calls/SKILL.md) | "Why did yesterday's calls fail?" | Finds failed calls, reads transcripts, summarizes an agent or a campaign |
| [omnidim-onboard-client](skills/omnidim-onboard-client/SKILL.md) | "Set up my new client and get them a number" | For resellers: creates and funds the client, runs its KYC where the region needs it, buys a number and attaches it |

## Tips from real calls

[What the best agents do](skills/omnidim-build-agent/references/call-tips.md), from answered calls of 200 agents in 86 accounts. The biggest one, for outbound calls: open with a name check of 8 words or fewer, like "Hi, am I speaking with [name]?". The median agent with a greeting that short lost 31% of its answered calls in the first 15 seconds, against 46 to 49% for greetings of 16 words or more.

## What is in this repo

```
skills/<skill>/SKILL.md       the steps your agent follows
skills/<skill>/references/    the guides those steps link to
.claude-plugin/               the Claude plugin manifest and icon
.mcp.json                     the hosted MCP server the plugin connects
```

## Where the text comes from

The skills are the same steps and guides the [OmniDimension MCP server](https://github.com/Omnidim/omnidim-mcp-cloud) serves, written out by its `scripts/export_skills.py`, along with the plugin manifest. Change them there, then rerun it on a clone of this repo.

## License

Apache 2.0
