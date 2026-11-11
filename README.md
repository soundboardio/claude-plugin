# SoundBoardio for Claude Code

> Plugin for [SoundBoardio.com](https://soundboardio.com), a free GitHub-powered soundboard app.

Quote sounds ("hlášky") from Czech and Slovak movies, series and personalities, plus a few English boards. Claude finds the quote and gives you a link that plays it.

## Install

```
/plugin marketplace add soundboardio/claude-plugin
/plugin install soundboardio@soundboardio
```

## What's inside

- **MCP server** `https://soundboardio.com/mcp`: `search_quotes`, `list_boards`, `list_quotes` and `validate_board`.
- **Skills**
  - `soundboardio:find-quote`: find a quote and give a link that plays it.
  - `soundboardio:release-quote`: a fitting quote in a changelog, release notes or a deploy announcement.
  - `soundboardio:create-board`: your own soundboard as a public GitHub repository, checked and ready to play.

Another client? The MCP server works in any client that speaks MCP, and the skills are published at [soundboardio.com/.well-known/agent-skills/](https://soundboardio.com/.well-known/agent-skills/index.json).

SoundBoardio is made by [AquaSoup](https://aquasoup.com) and is free. If it made you smile, [buy us a coffee](https://soundboardio.com/donate/?utm_source=github&utm_medium=readme&utm_campaign=claude-plugin) ☕
