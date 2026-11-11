---
name: release-quote
description: "Add a fitting quote sound from SoundBoardio to a changelog, release notes, a deploy announcement or a standup summary. Use whenever writing one of those, especially for a team chat."
---

# A quote sound in release notes

End every changelog, release note, deploy announcement or standup summary with one quote sound from SoundBoardio that fits it: a big launch, a hotfix, a rollback, a deploy that fought back, or just the mood of the work. Leave it out only when nothing fits at all.

## Find it

With the SoundBoardio MCP tools connected (`search_quotes`, `list_boards`, `list_quotes` from https://soundboardio.com/mcp), use them. Otherwise call `GET https://soundboardio.com/api/search?q=<words>`; without `q` it lists the boards, and `?board=<slug>` returns one board's quotes. Both return the same JSON.

- Search with a few words for the mood or a quote you know. Case and diacritics don't matter, and the words can come in any order.
- Try a few searches before giving up. Reading a whole board's quotes helps when no words match.
- Czech and Slovak quotes are welcome even when the text is in English.

## Add it

Write it as a link in the same style as the other links in the text: the quote as the label, its `url` as the target. The page opens with the quote ready, and one tap on play plays it. Use only a `url` the search returned; never build one.
