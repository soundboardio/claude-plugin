---
name: create-board
description: "Create a soundboard on SoundBoardio: a public GitHub repository soundboardio-<name> with the sounds, icons and soundboardio.json manifest, checked and ready to play. Use when someone wants to make, set up, fix or publish their own soundboard of quotes or sounds."
---

# Create a SoundBoardio board

A [SoundBoardio](https://soundboardio.com/) board is a plain public GitHub repository: sounds, icons and a `soundboardio.json` manifest. SoundBoardio reads it straight from GitHub, so there is nothing to build or deploy, and every push shows up on the board.

## Ask first

- What the board is about, its name and the language of its sounds.
- Where the sounds are: finished files, or a longer video or recording plus the moments to cut out.
- The text of every sound. Take it from the user or from a transcript, never make one up.
- The GitHub account or organisation that will own the repository, and the author name and link shown on the board.
- An image for the icon. Every board needs one; a still from the source video works.

## Repository

Name it `soundboardio-<name>`, with `<name>` in lowercase letters, digits and dashes, and make it public. Without the `soundboardio-` prefix SoundBoardio can't open it. Start from an empty repository:

```
soundboardio-<name>/
├── soundboardio.json
├── README.md
├── icons/
│   ├── icon-192.png
│   ├── icon-512.png
│   ├── icon-maskable-512.png
│   └── og.jpg
└── sounds/
    ├── first-quote.mp3
    └── second-quote.mp3
```

The board opens at `https://soundboardio.com/app/<owner>/<name>/`, without the prefix. The README needs only the board's name and that link.

## Sounds

One MP3 per tile. File names in lowercase ASCII with dashes: no spaces, and never `#`, `?` or `%`, which break the sound's URL.

Uploads of the same video and compilations often overlap: compare their lengths and transcripts first, and cut from the most complete, best-sounding one.

A tile plays the moment it is tapped, so cut the silence at the start, and keep the loudness even across the board:

```sh
# cut a clip out of a longer recording
ffmpeg -ss 00:01:23.40 -to 00:01:26.10 -i source.mp4 -vn clip.wav
# fade the ends, trim the silence, even out the loudness, hold the peaks down, mono 96 kb/s MP3
ffmpeg -i clip.wav -af "afade=t=in:d=0.01,afade=t=out:st=2.67:d=0.03,silenceremove=start_periods=1:start_threshold=-50dB,areverse,silenceremove=start_periods=1:start_threshold=-50dB,areverse,loudnorm=I=-16:TP=-1.5:LRA=11,aresample=44100,alimiter=limit=0.79:level=false:latency=true" -ac 1 -ar 44100 -b:a 96k sounds/first-quote.mp3
```

The fade-out starts 0.03 s before the clip ends (`st`, here 2.70 − 0.03), so a cut through background noise doesn't click. Keep the limiter: without it, resampling and the MP3 encoder push the peaks above 0 dB and some devices crackle. `loudnorm` can't measure a clip shorter than about 0.4 s: leave it out for those.

`silenceremove` trims only real silence. When music or room noise runs under the speech it trims nothing, so place the cut points yourself, in a gap between two words. `silencedetect` lists the gaps, with times counted from `-ss`:

```sh
ffmpeg -ss 60 -to 80 -i source.mp4 -af silencedetect=noise=-40dB:d=0.05 -f null -
```

When it finds no gap, the background is louder than `noise`: raise it to -30 or -25 dB. Cut in the middle of the gap nearest the word timestamps (see Text). Drawn-out last words are easy to clip and a start can catch the tail of the previous word: check both ends of every clip.

## Text

Every tile needs the words it plays. Take them from the user, or transcribe the source with a speech-to-text model that gives word timestamps (Whisper large-v3 works well); the timestamps also show where to cut. Then transcribe every cut clip again: extra words at either end mean the cut is off. Clips under about 2 s often come out garbled, so trust the long transcript for those. A model told to expect one language translates speech in another one: transcribe foreign lines in their own language. Never fill in a word you can't make out. Write `…` in its place in `lyrics`, make no tile of it, and give the user a list of the unclear spots: the source, the time and what it might be. When the speech mixes languages or dialect, ask once how to spell such words (say "good" or "gut") rather than deciding word by word.

## Icons

Every board needs icons: the validator fails a board without them. Start from one image of at least 512 × 512 px. When the user has none, a still from the source video works, or the video's thumbnail:

```sh
ffmpeg -ss 00:00:05 -i source.mp4 -frames:v 1 art.png
yt-dlp --skip-download --write-thumbnail --convert-thumbnails png -o art "<video url>"
```

Then make the icons from it:

```sh
ffmpeg -i art.png -vf "scale=192:192:force_original_aspect_ratio=increase,crop=192:192" icons/icon-192.png
ffmpeg -i art.png -vf "scale=512:512:force_original_aspect_ratio=increase,crop=512:512" icons/icon-512.png
# maskable: the picture inside the middle 80 %, the board color around it
ffmpeg -i art.png -vf "scale=410:410:force_original_aspect_ratio=increase,crop=410:410,pad=512:512:(ow-iw)/2:(oh-ih)/2:color=0x8b0000" icons/icon-maskable-512.png
# link previews and the Android install dialog
ffmpeg -i art.png -vf "scale=1200:630:force_original_aspect_ratio=increase,crop=1200:630" -q:v 3 icons/og.jpg
```

`crop` keeps the middle of the picture. When the face is off-centre, place it: `crop=512:512:X:Y`, with X and Y the top-left corner in the scaled picture. Without the 1200 × 630 image, link previews show the general SoundBoardio one.

## Manifest

`soundboardio.json` in the repository root:

```json
{
  "name": "Board name",
  "shortName": "Board",
  "description": "One line about the board",
  "story": [
    "What the board is and where its sounds come from, in a few sentences."
  ],
  "author": { "name": "Author", "url": "https://example.com" },
  "lang": "en",
  "color": "#8b0000",
  "version": "1.0.0",
  "rootUrl": "sounds/",
  "icons": [
    { "src": "/icons/og.jpg", "sizes": "1200x630", "type": "image/jpeg" },
    { "src": "/icons/icon-192.png", "sizes": "192x192", "type": "image/png", "purpose": "any" },
    { "src": "/icons/icon-512.png", "sizes": "512x512", "type": "image/png", "purpose": "any" },
    { "src": "/icons/icon-maskable-512.png", "sizes": "512x512", "type": "image/png", "purpose": "maskable" }
  ],
  "tiles": [
    { "name": "The quote as it is spoken", "filename": "first-quote.mp3" }
  ],
  "references": [
    { "name": "Where the sounds come from", "url": "https://example.com/source", "year": 2024 }
  ]
}
```

- `name` is the title on the board and in search results. `shortName` goes under the app icon on a phone: 12 characters at most.
- `description` is one short line, best a quote of about 40 characters. With `story`, a few paragraphs shown under the name, it makes the board's description in search results: the description, then the first sentence of `story`, within 160 characters, so a long description pushes the story out. Every fact in the story needs a source in `references`.
- `lang` is the two-letter code of the sounds' language; when they mix languages, the one most of the speech is in. `cs` and `sk` boards get the Czech interface, the rest the English one.
- `color` is the board's hex color. The text on it gets a readable color by itself.
- `rootUrl` is the sounds' folder in the repository, or a full `https://` address ending with `/` when the files are hosted elsewhere.
- `tiles` come in the order they show on the board, the best known first. `name` is the quote in the words that are spoken, slang and dialect included, and short: a tile fits about 25 characters in two lines. For a longer quote, give the tile its punchline as `name` and the whole quote in `lyrics` with a `transcript` (below). `filename` is the file in `rootUrl`. Optional `tags` are words shown on the tile that search finds too.
- `icons`: `src` is a path in the repository starting with `/`, or an `https://` address. `sizes` is written like `192x192`.
- `references` (optional) link the sources: the film, the video, the person. Each has a `name` and `url`, optionally a `year` (when the source came out) and a `note`.
- `lyrics` (optional) is the transcript of the source, shown under the tiles. One array item is one paragraph. When more than one person speaks, one item is one turn and every item starts with `– ` (an en dash and a space). Plain text, no HTML, and every item ends with punctuation. Give every tile a `transcript`, even when it is the same as `name`: the quote exactly as it is worded inside one lyrics item. Only a tile with a `transcript` is marked in the text and playable from it.

Straight double quotes in the JSON and no trailing commas.

## Check

The validator reads only the manifest, so first make sure that every tile's `filename` is in the sounds folder and every icon `src` exists. Then check the manifest before pushing:

- MCP: `validate_board` with `manifest` (the file's text) and `repo` (`<owner>/soundboardio-<name>`), from the SoundBoardio MCP server at https://soundboardio.com/mcp.
- HTTP: `curl -s -H "Content-Type: application/json" --data-binary @soundboardio.json "https://soundboardio.com/api/validate?repo=<owner>/soundboardio-<name>"`. Without the JSON content type the POST is refused.

The answer lists every check with its `status` and `line`. Fix every `error`. A `warning` is worth fixing, a `tip` is up to the user.

## Publish

```sh
git init -b main && git add . && git commit -m "First version"
gh repo create <owner>/soundboardio-<name> --public --source . --push
```

Without `gh`, create the empty public repository on github.com and push to it. Then check the pushed board: `validate_board` with just `repo`, or `GET https://soundboardio.com/api/validate?repo=<owner>/soundboardio-<name>`. Give the user the `board` link from the answer. GitHub serves a change up to five minutes late, so when a fix doesn't show, wait and check again.

People can do the same in the browser: the [generator](https://soundboardio.com/generator/) builds a manifest from dropped-in MP3s and the [validator](https://soundboardio.com/validator/) checks a repository.
