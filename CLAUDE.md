# Card Showdown — working context for Claude Code

## What this is
A single-file vanilla-JS web app: `index.html`. No framework, no build step, no package.json. All HTML + CSS + JS live in that one file inside `<style>` and `<script>` tags. Keep it that way unless there's a strong reason to split.

## How to work on it
- All app logic is in the `<script>` block; a global `S` object holds state, and a single `render()` function paints `#app` based on `S.phase` (`boot` / `setup` / `playing` / `stats` / `done` / `neterr`).
- Surgical DOM updates (by element id: `#rarList`, `#poolInfo`, `#startBtn`, `#setGrid`, `#artistList`, etc.) are used for filter toggles to avoid full re-render flicker. Follow that pattern for new in-place updates.
- After ANY change to the script, validate it before shipping:
  ```bash
  # extract the <script> and syntax-check it
  node -e "const fs=require('fs');const h=fs.readFileSync('index.html','utf8');const m=h.match(/<script>([\s\S]*)<\/script>/);fs.writeFileSync('/tmp/cs.js',m[1]);" && node --check /tmp/cs.js && echo OK
  ```

## Data sources
- Sets + cards: `https://raw.githubusercontent.com/PokemonTCG/pokemon-tcg-data/master` (`sets/en.json`, `cards/en/{setId}.json`). CORS-enabled.
- Prices: `https://api.pokemontcg.io/v2/cards?q=set.id:{id}&select=id,tcgplayer,cardmarket`. Rate-limited without an API key.

## Known constraints
- Clipboard image-copy and localStorage persistence need a **secure context** (https or localhost). They silently no-op on `file://` in some browsers.
- Artist-only mode currently loads ALL sets' card files to build the global artist index (heavy). With a pokemontcg.io API key this could instead query `q=artist:"X"` directly.

## Roadmap (prioritised)
1. **Host on GitHub Pages** (done / in progress).
2. **Community data via Supabase** — NEXT. See below.
3. **pokemontcg.io API key** — raises rate limits; enables prices in artist mode and instant artist/species lookup by query instead of bulk loading.
4. **Design polish** — carry the foil/grain language into set tiles; build a proper wordmark/logo.
5. Backlog: full ranking mode (merge sort), favourite-species-across-all-sets, shareable setup links (encode filters in URL), tap-and-hold zoom, type/era filters, stats export/import, PWA "add to home screen".

## Next task: Supabase community layer
Goal: show "X% of players picked this card" / a community percentile per card.

Sketch:
- Table `card_stats (card_id text primary key, wins int default 0, appearances int default 0)`.
- A Postgres function `record_match(winner text, loser text)` marked `SECURITY DEFINER` that upserts/increments both rows. Grant execute to `anon`.
- RLS: enable on the table; allow `select` to `anon` (read aggregates), block direct insert/update — all writes go through the function.
- Optional head-to-head table `matchups (a text, b text, a_wins int, total int)` keyed on a normalised pair if we want true "when shown against this card" stats.
- Client: keep the Supabase URL + anon key in the page (anon key is public by design; RLS protects data). On each pick call `record_match`; to show community %, query `card_stats` for the card and compute `wins/appearances` and a percentile across all cards.
- Watch for abuse (spam inflating counts); acceptable noise for a fun project, but consider a light rate limit later.
