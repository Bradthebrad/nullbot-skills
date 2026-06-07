# nullbot-skills

Reusable NullBot skills that can be installed from the NullBot marketplace.

Skills are plain directories containing a `SKILL.md` file. They do not grant new capabilities by themselves; they teach the agent how to use capabilities that are already available through configured tools and MCP servers.

## Skills

| Skill | Purpose |
| --- | --- |
| `mcp-skill` | Teaches NullBot how to design, build, document, release, and marketplace-register Go MCP servers using `tinychain/mcp`. |

## Layout

```text
skills/
  mcp-skill/
    SKILL.md
```

## Install

Once marketplace support is implemented in NullBot, this repo can be added as a `skill_pack` source.
