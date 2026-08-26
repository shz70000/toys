# Packaging Khata as an Android app

The app is a PWA. PWABuilder wraps it in a **Trusted Web Activity** — a native
shell that loads the live site. This file records how it was built and how to
change it later.

## Build settings that worked

| Field | Value |
|---|---|
| URL | `https://shz70000.github.io/toys/khata/` |
| Package ID | `com.mindmeldnexus.khata` |
| Signing key | Create new (keep the file — see below) |

Two mistakes that cost a build each:

- Pasting a **GitHub pull-request URL** instead of the app URL. The package
  then opens a GitHub page.
- A Package ID containing **hyphens**. It is a Java package name, not a
  description: lowercase letters, digits and underscores only, each segment
  starting with a letter. Descriptive text belongs in *App name*, where spaces
  and punctuation are fine.

## ⚠️ The signing key

The zip contains `signing.keystore` and `signing-key-info.txt`. **Back both up
before anything else.** They are the app's identity. Lose them and the app can
never be updated — only published again as a separate app that every user must
install from scratch. There is no recovery.

## Changing the app afterwards

Because it is a TWA, the APK contains no app content — it loads the live URL.

| Change | What to do |
|---|---|
| Features, layout, wording, logic | Edit `khata/`, bump `CACHE` in `sw.js`, push to `main`. Installed phones pick it up — **no rebuild, no reinstall.** |
| App name, icon, package ID, splash | Rebuild in PWABuilder and reinstall |

So day-to-day work never involves Android tooling at all.

## Does it still work offline?

Yes. `sw.js` caches the app shell on first run, and the ledger itself lives in
IndexedDB on the device. The app opens and works with no connection.

The one thing that needs the network is **picking up an update** — the phone
must be online once for the service worker to fetch the new version. Bumping
`CACHE` is what tells it a new version exists.

## Removing the address bar

A TWA shows a thin bar with the site's domain until the site proves it trusts
the app. To remove it:

1. Create a public repository named exactly **`shz70000.github.io`**
2. Add `.well-known/assetlinks.json` from the PWABuilder zip
3. Enable Pages on it (Settings → Pages → Source: GitHub Actions)
4. Confirm `https://shz70000.github.io/.well-known/assetlinks.json` loads
5. Reinstall the APK — Android re-checks on install

The file contains the SHA-256 fingerprint of the signing key, which is how
Android matches app to site.

**Without any website at all**, a TWA cannot hide that bar — the trust check
has nowhere to live. The alternative is **Capacitor**, which bundles the web
files *inside* the APK: no URL, no address bar, no hosting needed. It needs
Android Studio and the SDK (~2–2.5 GB), and updates then require rebuilding
and reinstalling rather than just pushing.

## Publishing to Google Play

Technically fine — Play accepts TWAs, and this is a genuine offline app rather
than a bookmark to a website, which is the distinction reviewers care about.

Practical requirements, all of which change over time, so **check the current
Play Console rules rather than trusting this list**:

- One-time developer registration fee (US$25 at time of writing)
- A privacy policy URL — straightforward here, since the app collects nothing
  and sends nothing anywhere
- The Data Safety form — again simple: no collection, no sharing
- Content rating questionnaire, store listing, screenshots, feature graphic
- An up-to-date target API level
- Upload the `.aab` from PWABuilder, not the `.apk`
- New **personal** developer accounts have additional closed-testing
  requirements before production access — a number of testers for a number of
  days. This is the biggest practical hurdle and the rule has changed more
  than once; verify it in Play Console before planning around it.

Sharing the APK file directly with people needs none of the above.
