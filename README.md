# Toys

This all about toys at very reasonable price with quick deliveries.

## Live site

Deployed automatically with GitHub Pages via GitHub Actions (see
`.github/workflows/static.yml`). Once Pages is enabled in the repo
settings (Settings → Pages → Source: **GitHub Actions**), the site is
live at:

```
https://shz70000.github.io/toys/
```

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
