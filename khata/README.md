# Khata — personal ledger (PWA)

Track money given to and received from each contact. Works offline, stores
everything on the device, installs to the home screen like a normal app.

## Deploy it (same as any GitHub Pages site)

1. Create a **public** repo, e.g. `khata`
2. Upload every file in this folder, keeping the names exactly as they are
3. Settings → Pages → Source: **GitHub Actions** (leave Custom domain empty)
4. Open `https://<your-user>.github.io/khata/` on your phone
5. Chrome menu → **Add to Home screen** — it now opens like an app, offline

> Must be served over **https** (GitHub Pages is), otherwise the offline
> service worker won't register.

## Files

| File | What it does |
|---|---|
| `index.html` | The whole app — screens, logic, styles, print layouts |
| `manifest.json` | Makes it installable (name, icons, colours) |
| `sw.js` | Caches the app so it opens with no internet |
| `icon-*.png` | Home-screen icons |

## How the balance works

Every entry is either **I GAVE** (money out) or **I GOT** (money in).

```
balance = total gave − total got
```

- balance **positive** → they owe you (receivable)
- balance **negative** → you owe them (payable)

The category (Loan, Repayment, Deposit, Receipt, Advance, Expense, Other) is
a label for filtering and reports — it never changes the maths. This keeps
the arithmetic impossible to get wrong.

## Backup

Settings → **Backup now** saves a `.json` file. On Android choose **Google
Drive** in the save/share sheet. Restore the same file on any device from
Settings → Restore.

The app nags you when a backup is overdue (daily by default — change it in
Settings). **Fully automatic daily upload to Drive is not possible from a
web app** — see "Limitations" below.

Safety: if saved data is ever unreadable, the app refuses to overwrite it,
parks a copy under a `khata_v1_damaged_…` key, and tells you — so a glitch
can never silently wipe your ledger.

## App lock (PIN)

Settings → **App lock** → Set a PIN. You're asked for it when the app opens,
and again if it has sat in the background over a minute.

The PIN itself is never stored — only a salted SHA-256 check value. That also
means **a forgotten PIN cannot be recovered**: the way back in is to clear the
app's site data and restore your backup file. Keep a backup.

> A PIN is a screen lock, not encryption. The ledger still sits in ordinary
> browser storage, so someone with your unlocked phone and technical knowledge
> could reach it. Your phone's own lock screen is the real protection.

## Sending a statement on WhatsApp

Party screen → **Send on WhatsApp** opens a chat with that contact,
pre-filled with their balance and recent entries. **Share / copy** does the
same through the normal share sheet (or the clipboard) for SMS, email, etc.

Set your **country code** in Settings so local numbers convert properly —
with `92`, a saved number of `0300-1234567` is sent as `923001234567`.
Numbers already stored as `+92…` or `0092…` are handled too.

## Printing / PDF

Party screen → **Print / PDF**, or Reports → **Print / PDF**.
In the print dialog choose **Save as PDF** for a PDF, or a printer for paper.
Statements include a running balance, totals and signature lines.

## Limitations (honest list)

- **No automatic background Drive upload.** Browsers don't let a web app run
  daily background jobs reliably. You get a reminder + one tap instead. True
  unattended backup needs a native Android app.
- **Data is per-browser.** Installing on a second phone starts empty — move
  data with a backup file.
- **Clearing browser site data erases the ledger.** Keep backups.
- The PIN locks the screen but does not encrypt the stored data.
- No fingerprint unlock, receipt photos, or interest calculation yet.

## Changing things

Colours live at the top of `index.html` in `:root`. Categories are the
`CATS` array near the top of the script. After changing any file, bump
`CACHE = "khata-v1"` in `sw.js` so phones fetch the new version.
