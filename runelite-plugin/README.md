# PocketGE Flip Tracker (RuneLite plugin)

Tracks your Grand Exchange flips **as they fill** — buys, sells, and profit
after the 2% GE tax — with one-click live price charts on
[pocketge.com](https://pocketge.com).

## What it does

- Listens to RuneLite's `GrandExchangeOfferChanged` events (all 8 slots) and
  records every **incremental fill** (partial fills included).
- Matches sells against earlier buys **FIFO per item** and books completed
  flips with profit **after tax** (same tax rules as the website: 2% =
  `floor(price/50)` per item, nothing under 50 gp, 5M cap, bond + classic
  tools exempt).
- Sidebar panel: session profit, recent flips, and a click-through to the
  PocketGE chart for any flipped item.
- **Local website bridge (opt-in, off by default):** serves your session's
  flips as JSON on `127.0.0.1` only, so pocketge.com open in *your* browser
  can display your live trades. Nothing ever leaves your machine — this is
  the Flipping-Copilot experience without a cloud account. CORS is locked to
  the PocketGE origins.

## Honest baseline rule

The first snapshot the tracker sees of an offer (e.g. on login, when the GE
replays slot state) sets a *baseline* without counting fills — only growth
the tracker actually witnesses is booked. No double counting, no guessing
about what happened while you were logged out.

## Build & run locally

Requires JDK 11 (matching RuneLite).

```
cd runelite-plugin
gradle wrapper          # once, if you don't have the wrapper committed
./gradlew build         # compiles + runs FlipTrackerTest
```

To run a full client with the plugin loaded, run
`PocketGeTrackerPluginTest.main()` from your IDE (standard RuneLite external
plugin workflow).

## Plugin Hub submission checklist

1. Move this folder to its own **public GitHub repo** (the Hub builds from a
   repo root — `build.gradle` at the top level).
2. Commit an `icon.png` at repo root (max 48×48, this one is copied from the
   site favicon — resize if needed).
3. Fork [runelite/plugin-hub](https://github.com/runelite/plugin-hub), add
   `plugins/pocketge-flip-tracker.properties`:
   ```
   repository=https://github.com/<you>/pocketge-flip-tracker.git
   commit=<full 40-char commit sha>
   ```
4. Open the PR. Automated checks build the plugin; a maintainer reviews.
   Disclose the local bridge in the PR description (it is opt-in, loopback
   only, and makes no outbound connections) — reviewers care about network
   behaviour, and this plugin makes **zero external requests**.

## Roadmap

- Persist flips across sessions (per RS profile, via `ConfigManager`).
- Website-side: read the local bridge and show live trades in the Bank of
  Gielinor panel.
- Optional cloud sync (Copilot-style) only if demand justifies a backend.
