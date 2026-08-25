# Toys

This all about toys at very reasonable price with quick deliveries.

## Live site

Deployed automatically with GitHub Pages via GitHub Actions (see
`.github/workflows/static.yml`). The site is live at:

```
https://shz70000.github.io/toys/
```

This address is free, comes with HTTPS automatically, and needs no setup
beyond Settings → Pages → Source: **GitHub Actions**.

### A note on custom domains

Settings → Pages has a "Custom domain" box. Leave it **empty** unless you
have actually bought a domain and pointed its DNS at GitHub. GitHub does
not verify that you own whatever you type there, so setting a domain you
don't own makes the site unreachable — visitors land on the registrar's
"this domain is for sale" page instead of these pages.

## Structure

- `index.html` — home page, links to the pages below
- `toys-love.html` — toy listings page
- `toystory-discount.html` — discounted toy listings page
- `style.css` — shared styling for all pages

## Deploying changes

Every push to `main` triggers the `Deploy static content to Pages`
workflow, which republishes the whole repo to GitHub Pages — no manual
build step needed. Just commit, push (or merge a pull request) to `main`,
and check the **Actions** tab to watch the deployment run.
