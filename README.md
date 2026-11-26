# OmniDimension skills

[![Release](https://img.shields.io/github/v/release/Omnidim/skills)](https://github.com/Omnidim/skills/releases)
[![License: Apache 2.0](https://img.shields.io/badge/license-Apache_2.0-blue)](./LICENSE)
[![Checked against the MCP server](https://github.com/Omnidim/skills/actions/workflows/check-against-server.yml/badge.svg)](https://github.com/Omnidim/skills/actions/workflows/check-against-server.yml)

These skills teach your AI assistant to build phone agents on [OmniDimension](https://omnidim.io). You describe the agent you want, and your assistant designs the call with you, sets up the voice and the number, and rings your phone so you can hear the agent before anyone else does.

Say you run a dental clinic. You tell Claude "build an agent that answers our phone and books cleanings". It asks you a few questions, shows you the call plan, connects your calendar, and calls you to test it. If something sounds off on that call, you tell it what to change.

## Install

**Claude Code** gets all six skills and the OmniDimension connection in one step:

```
/plugin install omnidim --marketplace Omnidim/skills
```

On Claude Code before version 2.1.275, run `/plugin marketplace add Omnidim/skills`, then `/plugin install omnidim@omnidim`.

**claude.ai and the Claude desktop app** (paid plans):

1. Open **Customize**, then **Plugins**.
2. Click **Add**, then **Add marketplace**, and enter `Omnidim/skills`.
3. Install **OmniDimension**.
4. On the plugin's **Connectors** tab, connect OmniDimension and sign in.

**Codex, Cursor, Gemini CLI, Copilot, OpenCode** and [more](https://github.com/vercel-labs/skills#readme):

```
npx skills add Omnidim/skills --skill '*' -g -y
```

**Any other assistant:** copy the folders in [`skills/`](skills) into its skills folder.

The skills work through the [OmniDimension MCP server](https://github.com/Omnidim/omnidim-mcp-server) at `https://mcp.omnidim.io/mcp`. You sign in with your OmniDimension account in the browser, so you need no API key. The Claude Code plugin adds the server for you: run `/mcp` once to sign in. In Cursor or VS Code, one click adds it:

[![Install in Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=omnidim&config=eyJ1cmwiOiJodHRwczovL21jcC5vbW5pZGltLmlvL21jcCJ9)
[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_omnidim-0098FF?logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?name=omnidim&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.omnidim.io%2Fmcp%22%7D)

Anywhere else, ask your assistant to "connect to OmniDimension" and the `omnidim-connect` skill walks it through. The [MCP docs](https://docs.omnidim.io/docs/mcp) have the steps for each app.

## Skills

In the order you usually need them:

| Skill | Ask your assistant | What it does |
|---|---|---|
| [omnidim-connect](skills/omnidim-connect/SKILL.md) | "Connect to my OmniDimension account" | Connects your account, read-only if you want |
| [omnidim-build-agent](skills/omnidim-build-agent/SKILL.md) | "Build an agent that books dental appointments" | Designs and builds the agent, gives it a number, tests it with a real call, and rolls a change back if it made things worse |
| [omnidim-run-campaign](skills/omnidim-run-campaign/SKILL.md) | "Call these 500 leads about the webinar" | Drafts the campaign, adds contacts, checks calling hours and how many calls run at once, then starts it when you say go |
| [omnidim-build-workflow](skills/omnidim-build-workflow/SKILL.md) | "After each call, send hot leads to Slack" | Builds, tests and turns on the automation |
| [omnidim-audit-calls](skills/omnidim-audit-calls/SKILL.md) | "Why did yesterday's calls fail?" | Finds the failed calls, reads the transcripts, and sums up an agent or a campaign |
| [omnidim-onboard-client](skills/omnidim-onboard-client/SKILL.md) | "Set up my new client and get them a number" | For resellers: creates and funds the client account, runs its KYC where the region needs it, and gets it a number |

Your assistant asks before anything that costs money, calls real people or changes an agent that's already live.

## A tip from real calls

For outbound calls, open with a name check of 8 words or fewer, like "Hi, am I speaking with [name]?". Then say who you are and why on the next turn. On the agents we measured, people hung up in the first 15 seconds far less often after a greeting that short. The [call tips guide](guides/guide/call-tips.md) has the numbers and a few more tips.

## What's in this repo

```
skills/<skill>/SKILL.md       when to use the skill, and which playbook to follow
skills/<skill>/<playbook>.md  the steps for one task
guides/                       short guides the playbooks link to: prompts, voices, numbers, campaigns, workflows
```

## Contributing

Found a step that's wrong or out of date? Open an issue or a pull request. Every change is checked against the OmniDimension MCP server before it merges.

## License

Apache 2.0
