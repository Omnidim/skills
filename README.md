# OmniDimension agent skills

Skills that teach an AI agent to build and run [OmniDimension](https://omnidim.io) voice agents through the [OmniDimension MCP server](https://github.com/Omnidim/omnidim-mcp-cloud). Each one follows the [Agent Skills](https://agentskills.io) format: a `SKILL.md` with the steps, and `references/` with the guides it needs.

| Skill | Use it to |
|---|---|
| [connect-omnidim](connect-omnidim/SKILL.md) | Connect an agent to your account, hosted or local |
| [build-agent](build-agent/SKILL.md) | Create a voice agent, give it a number, test it, roll it back |
| [build-workflow](build-workflow/SKILL.md) | Automate what happens after a call |
| [run-outbound-campaign](run-outbound-campaign/SKILL.md) | Call a list of people safely |
| [audit-calls](audit-calls/SKILL.md) | Find failed calls and summarize how calls went |

## Install

```bash
npx skills add Omnidim/skills
```

Add `--skill build-agent` for one skill. For Claude Code, you can also copy a skill folder into `~/.claude/skills/`.

The skills need the MCP server connected. The `connect-omnidim` skill has the steps, or see the [MCP docs](https://docs.omnidim.io/docs/mcp).

## Where the text comes from

The skills are the same steps the MCP server serves as prompts and guides, written out by `scripts/export_skills.py` in [omnidim-mcp-cloud](https://github.com/Omnidim/omnidim-mcp-cloud). Edit them there, not here, then regenerate.

## License

Apache 2.0
