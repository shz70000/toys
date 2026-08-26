# Project context

Read this first — it is the handover for anyone (human or Claude) picking this
repository up cold.

Owner: **shz70000** · Software owner: **MindMeld Nexus**

## What is in here

Two unrelated things share this repository:

| Path | What it is | Live at |
|---|---|---|
| `/` (root) | A small toy-shop site — affiliate product listings | https://shz70000.github.io/toys/ |
| `/khata/` | **Khata**, an offline personal ledger app (PWA) | https://shz70000.github.io/toys/khata/ |

The ledger lives here only because it needed a public HTTPS URL quickly, which
is the prerequisite for packaging it into an Android APK. It is independent of
the shop pages and can move to its own repository at any time — nothing in it
depends on the shop.

## Deployment

`.github/workflows/static.yml` publishes the whole repository to GitHub Pages
on every push to `main`. No build step. Settings → Pages → Source must stay on
**GitHub Actions**, and **Custom domain must stay empty** (see the history
section below for why).

## The Khata app

`khata/README.md` is the app's own documentation — features, storage,
backup format, letterhead, limitations. Read it before changing the app.

Key design decisions, so they are not accidentally undone:

- **Balance maths.** Every entry is `out` (I gave) or `in` (I got);
  `balance = out − in`. Positive means they owe the user. The category
  (Loan, Repayment, …) is only a filter label and must never affect arithmetic.
- **Storage is IndexedDB**, not localStorage — quota is ~950 MB rather than
  ~5 MB. Older localStorage data migrates on first run, and the old copy is
  deleted only after the new one is safely written. localStorage remains a
  fallback where IndexedDB is unavailable.
- **The PIN belongs to the device, never the data.** Backups are written
  without a PIN, and restoring never changes the PIN on the phone being
  restored onto. An earlier version got this wrong and could lock people out
  of their own device permanently.
- **Backups are AES-256-GCM**, key derived by PBKDF2-SHA256 at 250,000 rounds
  from a user-chosen password. There is no recovery path without that
  password, by design.
- **Nothing is ever written to the device unless the user taps.** No automatic
  downloads. An overdue backup shows a dismissible banner, never a popup.
- **Destructive actions use in-app sheets, not `confirm()`.** Some contexts
  silently suppress native dialogs, which once made "Erase all data" appear
  to do nothing.
- **App ownership vs user letterhead are separate.** `APP_NAME` / `APP_OWNER`
  near the top of the script are fixed (Khata by MindMeld Nexus, shown in
  Settings → About and as a small footer credit). Everything printed in the
  statement header — name, phone, email, logo — belongs to whoever is using
  the app and starts empty. No personal details are baked into the source.
- **All paths are relative** so the app works under the `/toys/` subpath.
  Absolute paths like `/style.css` will break it.

After changing any file in `khata/`, **bump `CACHE` in `khata/sw.js`** or
installed copies keep serving the old version.

## The Android app

Packaged with **PWABuilder** (pwabuilder.com) as a Trusted Web Activity — a
thin native shell that loads the live URL above.

- Package ID: `com.mindmeldnexus.khata` (permanent; changing it makes it a
  different app on Android)
- Package IDs must be valid Java identifiers — **no hyphens, no spaces**
- Because it is a TWA, content changes reach installed phones without
  rebuilding or reinstalling. Only the name, icon or package ID need a rebuild.

⚠️ The `signing.keystore` and its password from the PWABuilder zip are
**unrecoverable**. Without them the app can never be updated, only replaced.

### Outstanding

- The TWA shows a thin `shz70000.github.io` address bar. Removing it needs
  `assetlinks.json` (included in the PWABuilder zip) served from
  `https://shz70000.github.io/.well-known/assetlinks.json`, which requires a
  repository named exactly `shz70000.github.io`.

## History worth knowing

- The Pages **custom domain** was once set to `toyshero.com`, a domain not
  registered to this account. GitHub does not verify ownership of what is
  typed there, so the site deployed successfully while every visitor —
  including via the default `github.io` URL, which redirects — landed on the
  registrar's parking page. Leave that box empty.
- Both shop pages were originally saved from a browser and referenced local
  `_files/` folders that were never committed, so every image and stylesheet
  was broken. They now point at their original CDN URLs.

## Known limitations (deliberate, not bugs)

- No automatic background upload to Google Drive. Browsers cannot run reliable
  daily background jobs; the user backs up with one tap instead. True
  unattended backup would need a fully native app.
- Data is per-device. Moving to another phone means restoring a backup file.
- Clearing the browser's site data erases the ledger.
- The PIN is a screen lock; it does not encrypt data at rest on the device.
  Backup files are properly encrypted.
- Printed PDFs cannot be password protected — the browser's own Save-as-PDF
  has no way to set one.

## Ideas not yet built

Receipt photos, partial settle-up, interest on loans, Urdu language toggle,
statement numbers, amount in words, ageing of unpaid items.
