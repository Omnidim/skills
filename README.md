# OmniDimension agent skills

Skills that teach an AI agent to build and run [OmniDimension](https://omnidim.io) voice agents through the [OmniDimension MCP server](https://github.com/Omnidim/omnidim-mcp-cloud). Each one follows the [Agent Skills](https://agentskills.io) format: a `SKILL.md` with the steps, and `references/` with the guides it needs.

| Skill | Use it to |
|---|---|
| [omnidim-connect](omnidim-connect/SKILL.md) | Connect an agent to your account, hosted or local |
| [omnidim-build-agent](omnidim-build-agent/SKILL.md) | Create a voice agent, give it a number, test it, roll it back |
| [omnidim-run-campaign](omnidim-run-campaign/SKILL.md) | Call a list of people safely |
| [omnidim-build-workflow](omnidim-build-workflow/SKILL.md) | Automate what happens after a call |
| [omnidim-audit-calls](omnidim-audit-calls/SKILL.md) | Find failed calls and summarize how calls went |

## Tips from real calls

[What the best agents do](omnidim-build-agent/references/call-tips.md), from answered calls of 200 agents in 86 accounts. The biggest one, for outbound calls: open with a name check of 8 words or fewer, like "Hi, am I speaking with [name]?". The median agent with a greeting that short lost 31% of its answered calls in the first 15 seconds, against 46 to 49% for greetings of 16 words or more.

## Install

```bash
npx skills add Omnidim/skills
```

Add `--skill omnidim-build-agent` for one skill. For Claude Code, you can also copy a skill folder into `~/.claude/skills/`.

The skills need the MCP server connected. The `omnidim-connect` skill has the steps, or see the [MCP docs](https://docs.omnidim.io/docs/mcp).

## Where the text comes from

The skills are the same steps and guides the MCP server serves, written out by `scripts/export_skills.py` in [omnidim-mcp-cloud](https://github.com/Omnidim/omnidim-mcp-cloud). Edit them there, not here, then regenerate.

## License

Apache 2.0
