# Slidum skills

Agent skills from [Slidum](https://slidum.com) — the player for AI-made
presentations. Teach your coding agent (Claude Code and compatible harnesses)
to build decks that play natively on Slidum: arrow keys, fullscreen, filmstrip,
sharing by link.

## Skills

### `slidum-deck`

Generate single-file HTML presentations that play natively on Slidum — and fix
existing decks flagged as **"Navigates on its own"** (passthrough mode, no
filmstrip). One self-contained `.html`, zero remote dependencies, no navigation
`<script>`; the player drives.

→ [`skills/slidum-deck/SKILL.md`](skills/slidum-deck/SKILL.md)

## Install

Pick whichever fits your agent:

```sh
# skills.sh — works across ~70 harnesses
npx skills add slidum/skills

# dedicated installer (this repo's skill only)
npx slidum-deck                        # this project (./.claude/skills)
npx slidum-deck --global               # every project (~/.claude/skills)
npx slidum-deck --dir ~/.codex/skills  # any other agent's skills directory
```

Claude Code users can also install it as a plugin, with managed updates:

```
/plugin marketplace add https://slidum.com/claude-marketplace.json
/plugin install slidum-deck@slidum
```

No terminal around? Every skill here is also a copy-paste prompt at
[slidum.com/skill](https://slidum.com/skill).

## Use

Ask your agent for a presentation — *"make me a deck for Slidum"* — and upload
the generated `.html` at [slidum.com](https://slidum.com) to play and share it.

## License

[MIT](LICENSE) © Slidum
