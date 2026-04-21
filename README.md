# Data Branch — STL Chess Club Site

This branch stores image assets for the tournament site. It has no relation to the application source code.

## Contents

```
logo.png          ← Club / tournament logo shown in the site header
sponsors/
  *.png           ← Sponsor logos shown in the site footer
```

## How to update images

1. Replace or add image files in this branch and push to `origin/data`.
2. Copy the updated files into `public/` (or `public/sponsors/`) in the `main` branch.
3. Commit to `main` and push — GitHub Actions will redeploy the site automatically.

## Adding a new sponsor

1. Add the sponsor PNG to `sponsors/` in this branch.
2. In `main`, copy the file to `public/sponsors/` and add an entry to `tournament.config.js`:
   ```js
   { name: 'Sponsor Name', logo: 'sponsors/sponsor.png', url: 'https://sponsor.com' }
   ```

## PGN files

PGN round files live in `main` under `public/pgn/` — **not here**. They are polled live every 30 seconds by the deployed site, so updating them requires a push to `main` (and an automatic redeploy).
