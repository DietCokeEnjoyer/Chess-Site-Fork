# Chess Tournament Site

A Vite + React website for displaying chess tournament standings and pairings.
Deployed automatically to GitHub Pages on every push to `main`.

## How it works

| Step | What happens |
|------|--------------|
| 1 | Tournament organizer uploads a `.pgn` file to `public/pgn/` |
| 2 | A push to `main` triggers the GitHub Actions workflow |
| 3 | The build script generates `public/pgn/manifest.json` from all PGN files |
| 4 | Vite builds the site; GitHub Pages deploys it |
| 5 | Visitors see updated standings and pairings automatically |

## PGN file conventions

- Place files in `public/pgn/` (e.g. `round1.pgn`, `round2.pgn`)
- Files are sorted alphabetically — name them so alphabetical order matches round order
- One or more games per file; each game is a standard PGN game block
- **Bye** — set the opponent's name to `BYE` (case-insensitive)
- **Forfeit** — add `[Termination "Forfeit"]` to the game headers

## Tournament Director — adjusting results

On the **Rounds** view, every game card has an **Edit** button.
Click it to override the result for that game.
Adjustments are stored in your browser's `localStorage` and reflected immediately in standings.
Adjusted results are marked with `*` on the card.

## Setup for a new tournament

1. **Fork** (or use as a template) this repository into your GitHub organisation
2. In the forked repo go to **Settings → Pages** and set the source to **GitHub Actions**
3. Open `tournament.config.js` and set your tournament name, colors, and logo
4. Upload PGN files to `public/pgn/` and push — the site deploys automatically

## Local development

```bash
npm install
npm run dev     # starts Vite dev server at http://localhost:5173
```

```bash
npm run build   # production build → dist/
npm run preview # preview the production build locally
```

## Project structure

```
public/
  pgn/
    manifest.json   ← auto-generated; do not edit manually
    round1.pgn      ← upload PGN files here
    round2.pgn
src/
  components/
    GameCard.jsx    ← single game card with edit support
    Rounds.jsx      ← round selector + game grid
    Standings.jsx   ← points table
  hooks/
    useTournamentData.js  ← fetches and parses PGN data
  utils/
    pgnParser.js    ← PGN parsing + standings calculation
  App.jsx
scripts/
  generate-manifest.js  ← prebuild script; auto-discovers PGN files
.github/workflows/deploy.yml  ← GitHub Actions CI/CD
```

---

## What we built and how to test it

Here's a walkthrough of everything the site does and how you can verify it's all working. You don't need to deploy anything — just run it locally.

### Getting it running

```bash
npm install
npm run dev
```

Go to **http://localhost:5173**. You'll land on the Standings tab with sample data from a 3-round tournament already loaded. This is what the site looks like for anyone visiting during a real tournament.

---

### Standings

The standings table is the first thing people see. It ranks every player by their total points — wins are worth 1, draws 0.5, byes 1, losses 0. Click the **Standings** tab and you should see all six sample players listed in order with their points, wins, draws, and losses. The top three get a gold, silver, and bronze badge.

To test that standings actually update, jump ahead to the TD adjustments section, change a result, and come back here — you'll see the numbers change in real time.

---

### Rounds

Click **Rounds**. You'll see three tabs along the top — Round 1, Round 2, Round 3. Each tab shows the game cards for that round: who played white, who played black, and what the result was.

A few things to look for:
- **Round 1** has a pending game (Frank Chen vs Grace Taylor). That card shows *In Progress* and there's a yellow banner at the top of the page saying the round isn't over yet.
- **Round 3** has a forfeit (Dave Wilson over Bob Jones) and a bye (Eve Martinez). These show different badge colors so they're easy to tell apart at a glance.

---

### TD result adjustments

This is the tournament director's tool for correcting mistakes or entering results that aren't in the PGN yet.

1. Go to **Rounds** and pick any round.
2. Click **Edit** on any game card.
3. Use the dropdown to pick a different result — try changing a win to a Draw.
4. Hit **Save**.
5. Now go back to **Standings**. Alice's or Bob's points (whoever was in that game) will have changed.
6. Go back to **Rounds**, hit **Edit** on that same card, and click **Reset** to put it back the way it was.

Adjusted results are marked with a `*` so it's obvious something was manually changed. These adjustments live in your browser and survive page refreshes, so the TD can close and reopen the tab without losing their work.

---

### Adding a new round

This simulates what happens when a tournament organizer uploads the next round's PGN file.

1. Stop the dev server if it's running.
2. Add a new file — say `public/pgn/round4.pgn` — with at least one game in standard PGN format. Use the existing round files as a reference.
3. Run `npm run dev` again.
4. A **Round 4** tab will appear automatically. No code changes, no config edits.

That's exactly how it works in production too — organizers push the file to GitHub and the site redeploys with the new round showing up on its own.

---

### Customising the look

Open `tournament.config.js`. While the dev server is running, try these changes and watch the browser update instantly:

- Set `name: 'My Tournament'` — the header title changes.
- Change `accent` to `'#cc2936'` — all the gold highlights turn red.
- Set `logo: '/logo.png'` — the chess piece icon in the header is replaced by your logo (the ACF logo is already in `public/`).
- Add a sponsor: `sponsors: [{ name: 'ACME', logo: '/logo.png' }]` — a sponsor row appears in the footer.

---

### Testing the production build

When you're done, run:

```bash
npm run build
npm run preview
```

Open **http://localhost:4173**. This is the exact bundle that GitHub Actions deploys to GitHub Pages — same behavior, no dev tooling. If it works here, it'll work live.
