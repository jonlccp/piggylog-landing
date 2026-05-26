# PiggyLog landing page

A self-contained static landing page for PiggyLog. Just HTML + CSS +
two PNGs. No build step, no framework, no JavaScript runtime
dependencies. Brand palette mirrored from
[`apps/mobile/src/tamagui.config.ts`](../apps/mobile/src/tamagui.config.ts).

## Why this exists

Primarily for **store-submission compliance**: Google Play and
Amazon Appstore both require a publicly-accessible privacy policy
URL. The privacy policy here is the HTML-rendered version of
[`docs/privacy.md`](../docs/privacy.md) (the canonical source).

The landing page itself (`index.html`) is a small marketing surface
&mdash; just enough to look legitimate to a store reviewer or a
parent browsing pre-install.

## Files

| File           | Purpose                                                                     |
| -------------- | --------------------------------------------------------------------------- |
| `index.html`   | Marketing landing. Logo, hero, three bucket-feature cards, privacy callout. |
| `privacy.html` | Privacy policy, rendered from `docs/privacy.md`.                            |
| `style.css`    | Shared styles. Uses the same `piggy*` palette as the mobile app.            |
| `icon.png`     | App icon (copy of `apps/mobile/assets/icon.png`).                           |
| `pip.png`      | Pip-the-mascot placeholder (copy of `apps/mobile/assets/pip-idle.png`).     |

No external requests: no Google Fonts, no CDN-hosted icon library,
no analytics. The page loads fully from the same origin so it works
on flaky networks and respects the privacy stance of the underlying
product.

## Local preview

Any static-file server works. Quick options:

```bash
# Python 3
python3 -m http.server --directory landing 8080

# Node (npx)
npx serve landing
```

Then open <http://localhost:8080>.

## Deploying to GitHub Pages

Two reasonable paths. Pick one.

### Path A — separate repo (recommended for cleanliness)

1. Create a new public GitHub repo (e.g. `piggylog-landing` or, if you
   want the root URL `https://<your-username>.github.io`, name it
   `<your-username>.github.io`).
2. Copy the entire `landing/` directory's **contents** (not the folder
   itself) to the root of that repo.
3. Push to `main`.
4. In the repo's **Settings &rarr; Pages**, set the source to
   `Deploy from a branch` &rarr; `main` &rarr; `/ (root)`.
5. The page goes live at the URL shown on the Pages settings page
   (usually `https://<your-username>.github.io/piggylog-landing/`,
   propagation takes a few minutes).

### Path B — `gh-pages` branch in the main repo

1. From the repo root:

   ```bash
   git checkout --orphan gh-pages
   git rm -rf .
   cp -R landing/* .
   git add -A
   git commit -m "Initial GitHub Pages deploy"
   git push -u origin gh-pages
   git checkout main
   ```

2. In **Settings &rarr; Pages**, set source to
   `Deploy from a branch` &rarr; `gh-pages` &rarr; `/ (root)`.

3. URL: `https://<your-username>.github.io/piggylog/`.

### Custom domain (optional)

If you reserve `piggylog.app` (or similar), add a `CNAME` file at the
root with the domain on a single line, and configure DNS to
`<your-username>.github.io`. See GitHub's
[custom-domain docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site).

## Updating the privacy policy

The canonical source is [`docs/privacy.md`](../docs/privacy.md). When
that changes:

1. Update `privacy.html` to match the new content. The conversion is
   manual (no build step) &mdash; mirror the section structure and
   keep the wording identical to avoid legal drift between the
   in-app, on-device, and on-website versions.
2. Update the `Effective date` and `Version` at the top of
   `privacy.html` to match.
3. Bump `POLICY_VERSION` in
   [`apps/mobile/src/privacy.ts`](../apps/mobile/src/privacy.ts) if
   the change is substantive (not just typo fixes).
4. Re-deploy GitHub Pages (just push the new commit; Pages re-deploys
   automatically on push to the configured branch).

## Outstanding TODOs

- The "Coming soon to Amazon Appstore & Google Play" callout in
  `index.html` should become actual store-listing links once
  submissions are approved.
- The Pip mascot is a placeholder pig illustration (see
  `apps/mobile/assets/design/pip-idle.svg`). When an illustrator
  delivers the final Pip, re-export and replace `pip.png` here.
- App icon (`icon.png`) is also a placeholder &mdash; refine the
  source SVG in `apps/mobile/assets/design/icon.svg` and regenerate
  before public launch.
- Add Open Graph card image: the current `og:image` reuses `icon.png`
  which works but isn't optimized for social-share dimensions
  (1200&times;630 is the modern standard). Low priority pre-launch.

## What's intentionally NOT here

- No newsletter / waitlist signup form. No need without a backend.
- No analytics. The privacy stance is a feature of the product;
  the marketing site should not contradict it.
- No JavaScript. Page works with JS disabled. Smaller attack surface,
  faster load, no privacy concerns.
- No FAQ / press / about pages. Add when there's actual demand for
  them.
