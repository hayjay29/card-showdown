# Card Showdown

A head-to-head bracket for finding your favourite Pokémon **card** (specific printings / art), not just your favourite Pokémon. Two cards face off, you tap the one you like more, winners advance until one card is crowned.

It's a single self-contained `index.html` — no build step, no dependencies. Open it in a browser and go.

## Features

- Multi-set selection via a **visual logo picker** (search + tap), plus rarity, searchable artist, and max-price filters.
- **Artist-only mode** — skip sets entirely and battle one illustrator's cards across every set.
- Pokémon-only toggle and a bracket size cap (8 / 16 / 32 / 64 / whole pool).
- Matchup screen with side-by-side cards (name, rarity, price + source, set #, illustrator), undo, and **share-as-image** (copies to clipboard).
- Champion screen: holographic tilt, round-by-round standings, full "what beat what" match log, and the card's all-time stats + your personal percentile.
- **Persistent stats dashboard** (localStorage): favourite illustrators, sets, rarities, and cards, with totals.

## Run locally

Just open `index.html` in a browser. For features that need a secure context (clipboard image-copy, reliable localStorage), serve it over `http://localhost` or `https`:

```bash
# any static server works, e.g.
npx serve .
# then open the printed http://localhost:... URL
```

## Deploy to GitHub Pages

1. Push this folder to a repo (e.g. `card-showdown`).
2. Repo → **Settings → Pages** → Source: **Deploy from a branch** → Branch: `main` / `/ (root)` → Save.
3. Live at `https://<username>.github.io/card-showdown/`.

Because Pages serves over **https**, clipboard image-copy and stat persistence work properly there.

## Data & attribution

- Card metadata and images: the open **Pokémon TCG dataset** (`PokemonTCG/pokemon-tcg-data`) and `pokemontcg.io`.
- Prices: approximate market value from TCGplayer (USD) or Cardmarket (EUR) via the pokemontcg.io API.
- Pokémon and card art are © Nintendo / Creatures / GAME FREAK. This is a free, non-commercial fan tool.

## Roadmap

See `CLAUDE.md` for the working context and the prioritised next steps (community data via Supabase is next).
