---
name: find-quote
description: "Find quote sounds (\"hlášky\") from Czech and Slovak movies, series and personalities, plus a few English boards, on SoundBoardio, and give a link that plays the quote. Use when someone wants to find, hear, play or share a quote sound, or asks which soundboard has a quote."
---

# SoundBoardio

[SoundBoardio](https://soundboardio.com/) hosts soundboards: grids of short sound clips, each labelled with the quote it plays. Most boards are Czech; there are Slovak and English ones too.

## Find a quote

If the SoundBoardio MCP tools are connected (`search_quotes`, `list_boards`, `list_quotes` from https://soundboardio.com/mcp), use them: they take the same parameters and return the same JSON as the API below. Otherwise call the API:

`GET https://soundboardio.com/api/search?q=<words>`

- `q`: words from the quote. Case and diacritics don't matter, and the words can come in any order.
- `lang` (optional): `cs`, `sk` or `en`, to search only the boards in that language.
- `board` (optional): a board's `slug`, to search only that board.
- `limit` (optional): how many quotes to return, 10 by default and 50 at most.

The response lists the matching `quotes`, whole-phrase matches first, and the `boards` whose name matches:

```json
{
  "query": "…",
  "total": 1,
  "quotes": [
    {
      "quote": "…",
      "transcript": "…",
      "url": "https://soundboardio.com/app/<slug>/#<hash>",
      "board": { "slug": "<slug>", "name": "…" },
      "lang": "cs"
    }
  ],
  "boards": []
}
```

A quote's `url` opens its board with that quote ready, and one tap on play plays it. Give the user this link.

## When nothing matches

The search matches words, not meaning. When the user paraphrases a quote or remembers it only roughly:

1. Try again with fewer, more distinctive words.
2. `list_boards`, or `GET https://soundboardio.com/api/search` with no parameters, lists every board with its `slug`, name, language and number of quotes.
3. `list_quotes`, or `GET https://soundboardio.com/api/search?board=<slug>`, returns all the quotes of one board with their links. Pick the one the user means.

Each board also has a Markdown page (`markdown` in the board list) with its story and sources.
