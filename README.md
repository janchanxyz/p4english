# 小四英文拼字 · p4english

Cute, colourful **Primary 4 English spelling game** for Hong Kong kids — mobile & iPad friendly.

**Live:** https://p4english.chanplanet.workers.dev/

## Features

- **每日 10 個字** — same words for everyone on the same Hong Kong calendar day (`Asia/Hong_Kong`)
- Each word hides **2–5 letters**; fill the blanks with an on-screen keyboard
- **繁體中文（香港）** gloss for every item
- Illustrations from **Wikimedia Commons / Openverse** (CC-friendly), shown via same-origin `/api/img` proxy
- **Shareable PNG result card** (Web Share API + download / clipboard fallback), spoiler-free like bird/fish
- Progress saved in `localStorage` for the same day
- **Edge TTS** — word + example audio pre-generated with Microsoft `edge-tts` (`en-GB-SoniaNeural`), shipped as static MP3s under `/audio/w/` and `/audio/e/` (no Cloudflare Workers AI at runtime)

## Vocabulary source (EDB honesty)

Hong Kong’s Education Bureau does **not** publish a separate Primary 4–only English wordlist. Official indicative targets are about **1,000 words by end of KS1** and **2,000 by end of primary (KS2)**. The interactive Wordlists package (*Enhancing English Vocabulary Learning and Teaching at Primary Level*, formerly at `cd1.edb.hkedcity.net/cd/eng/vocab09`) is thematic/alphabetical and not a stable machine-readable download.

This app uses a **curated P4-appropriate pool** (~500 spelling words, length 4–12 letters) aligned with EDB primary themes (animals, food, school, body, clothes, weather, nature, transport, places, home, jobs, sports, imageable verbs, etc.). Prefer concrete/imageable items so pictures make sense. **Not an official EDB dump** — for classroom practice / home fun.

References:

- [EDB primary vocabulary resources](https://www.edb.gov.hk/en/curriculum-development/kla/eng-edu/references-resources/resource%20pri_vocab.html)
- [Wordlists preamble (archive)](https://www.edb.gov.hk/en/curriculum-development/kla/eng-edu/references-resources/Wordlists_preamble.html)

## Develop

```bash
npm install
python3 scripts/build_words.py      # rebuild src/words-data.json
python3 scripts/enrich_images.py    # Wikimedia / Openverse images
python3 -m venv .venv && .venv/bin/pip install edge-tts
.venv/bin/python scripts/generate_static_tts.py
npm run dev
```

## Deploy (Cloudflare Workers)

```bash
npm run build
npx wrangler deploy
```

- Worker name: **`p4english`**
- Assets SPA from `dist/`
- Expected URL: **https://p4english.chanplanet.workers.dev/**

## Tech

Vite + vanilla JS, `wrangler.jsonc` SPA + `run_worker_first: ["/api/*"]`, Worker image proxy for canvas CORS.

Daily seed key: `p4english-daily-YYYY-MM-DD`.

## TTS (static Edge MP3s)

Word and example audio are pre-generated with Microsoft `edge-tts` (`en-GB-SoniaNeural`) into `public/audio/w/` and `public/audio/e/`, then shipped with the Vite build. `/api/speak` returns **410 Gone** — runtime does not use Workers AI or `TTS_CACHE` KV.

```bash
.venv/bin/python scripts/generate_static_tts.py
```
