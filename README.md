# rawrep.app — static marketing site

Four hand-written pages, no build step. Open `index.html` in a browser to preview.

```
index.html          landing page
privacy.html        privacy policy      (text reviewed — do not rewrite)
support.html        support FAQ         (text reviewed — do not rewrite)
terms.html          terms of use        (text reviewed — do not rewrite)
styles.css          the whole design system (tokens, device frames, layout)
favicon.svg         app mark, colourway B (ADR-023)
CNAME               rawrep.app
img/                app screenshots (WebP) + Open Graph card
fonts/              Space Grotesk Medium, subset to numerals (SIL OFL 1.1)
```

## Design rules this site follows

Same rules as the app (PRD §6.2, ADR-014, ADR-017, ADR-023), so the site and the
product read as one thing:

- Dark only. `#0B0B0D` page, `#16161A`/`#1B1B21` surfaces, `#F2F2F0` text, `#8A8A93` secondary.
- Lime `#C6FF00` is the only accent, and only for the ✓, the timer, and the call to action.
- **Space Grotesk 500 is for numerals only** (class `.n`). Every word is SF Pro via `-apple-system`.
- Numerals are tabular; ≥48 px numerals get −2 % tracking.

## Regenerating the assets

Screenshots come from `docs/reviews/2026-08-29-visual/` (iPhone 16 Pro, 552 × 1200):

```sh
cwebp -q 82 -m 6 -sharp_yuv ../reviews/2026-08-29-visual/05-active-workout.png -o img/workout.webp
```

The numeral font is subsetted from the font the app bundles, so the digits on the
site are literally the digits in the app:

```sh
pyftsubset ../../Overload/Resources/Fonts/SpaceGrotesk-Medium.ttf \
  --output-file=fonts/space-grotesk-500-numerals.woff2 --flavor=woff2 \
  --text='0123456789.,:$×/-–— ' --layout-features='tnum,kern,liga' \
  --no-hinting --desubroutinize
```

`fonts/OFL-SpaceGrotesk.txt` must ship alongside it — the OFL requires the notice
to travel with the font.

The Open Graph card (`img/og.png`, 1200 × 630) is rendered from a throwaway HTML
file through headless Chrome so it uses the real typefaces. Re-render it only if
the headline changes.

## Checks before deploying

```sh
tidy -q -e index.html privacy.html support.html terms.html    # expect no errors
```

Widths to eyeball: 390, 768, 1280. Note that headless Chrome clamps its window to
a 500 px minimum, so a true 390 px render needs the page in a 390 px-wide iframe.

## GitHub Pages deploy

1. Push this repo (or a standalone `rawrep-site` repo containing just this folder's contents at its root) to GitHub.
2. In the repo's Settings → Pages, set Source to "Deploy from a branch", branch `main`, folder `/docs/site` (or `/` if using the standalone repo).
3. Leave `CNAME` (`rawrep.app`) in place — GitHub Pages reads it automatically; do not delete it.
4. At your DNS provider, point `rawrep.app` at GitHub Pages: an `A` record to GitHub's Pages IPs (185.199.108.153, .109.153, .110.153, .111.153), or a `CNAME`/`ALIAS` record to `<username>.github.io` if using a subdomain.
5. Back in Settings → Pages, wait for the custom domain to verify, then check "Enforce HTTPS" — required, since `.app` domains are HSTS-preloaded and will not load over plain HTTP.
6. Visit `https://rawrep.app` once DNS propagates (can take up to 24h) to confirm.
