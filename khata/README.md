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

## Backup — encrypted, and only when you ask

Settings → **Backup now**. You choose a password; the file is encrypted with
**AES-256-GCM**, the key stretched from your password by **PBKDF2-SHA256**
(250,000 rounds). The saved `.json` holds only ciphertext, so it is safe to
keep in Google Drive, email it, or store on a shared PC.

**Nothing is ever written automatically.** A file is only created when you tap
the button. If a backup is overdue you get a small dismissible banner on Home —
never a popup, never a silent download.

To restore on any phone: Settings → **Restore from backup file** → pick the
file → enter the password. Wrong passwords simply re-prompt.

Saved files are timestamped to the second (`khata-backup-2026-08-25_221407.json`),
so backing up twice in a row never overwrites the earlier file. Printed
statements are named the same way, per contact.

If you let the browser generate and save the password, **check your own copy
too**: after a backup the app shows the password with a Copy button, because
password managers do not always offer a saved password back later.

> **⚠️ Lose the password and the backup is unreadable — permanently.** That is
> what encryption means; there is no recovery route, for you or anyone else.

CSV export is separate and deliberately **not** encrypted, because spreadsheets
have to be able to open it. The app warns you before writing one.

## App lock (PIN)

Settings → **App lock** → Set a PIN. You're asked for it when the app opens,
and again if it has sat in the background over a minute.

The PIN itself is never stored — only a salted SHA-256 check value. That also
means **a forgotten PIN cannot be recovered**: the way back in is to clear the
app's site data and restore your backup file. Keep a backup.

The lock belongs to the device, not to the data: backups do not carry a PIN,
and restoring one never changes the PIN set on the phone you restore onto.

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

## Your letterhead

Settings → **Your details on statements**. These belong to whoever is using
the app and start empty, so each person fills in their own:

| Field | Where it prints |
|---|---|
| Logo | Top, centred |
| Your name or business name | Heading, and "for <name>" under the signature |
| Phone · Email | One line beneath, blanks skipped |
| Signature name (optional) | Under the signature line |

Anything left blank is left off — a statement prints fine with none of it set.

Logos are resized to fit 360×140 and stored as PNG (usually a few KB).
Anything still over 300 KB is rejected with a message rather than saved. The
logo travels inside your encrypted backup, so restoring on a new phone brings
it back.

## Who owns what

Two separate things, deliberately kept apart:

- **The app** is © MindMeld Nexus. That is fixed in `APP_NAME` / `APP_OWNER`
  near the top of the script, shown in Settings → About, and printed as a small
  "Khata by MindMeld Nexus" credit at the foot of each statement.
- **The ledger and the letterhead** belong to the person using the app. No
  personal details are baked into the source.

## Erasing data

Settings → **Erase all data** is the only irreversible action. It asks in an
in-app sheet (never a browser popup, which some contexts suppress), offers to
back up first, requires you to type **ERASE**, and then asks for your PIN if one
is set. Nothing is deleted until all of that passes.

## Running inside a preview window

If the app is opened inside a preview frame rather than at its own web address,
the browser blocks printing and restricts file saving. The app now says so
instead of appearing to do nothing. Install it from your own Pages URL and
Print/PDF, CSV export and backups all behave normally.

## Limitations (honest list)

- **No automatic background Drive upload.** Browsers don't let a web app run
  daily background jobs reliably, and writing files without asking would be
  wrong anyway. You get a dismissible banner and back up with one tap. True
  unattended backup needs a native Android app.
- **Data is per-browser.** Installing on a second phone starts empty — move
  data with a backup file.
- **Clearing browser site data erases the ledger.** Keep backups.
- The PIN locks the screen but does not encrypt the data held on the device;
  backup files, however, are properly encrypted.
- After 5 wrong PIN attempts the keypad locks for 5s, doubling each further
  failure up to 5 minutes.
- Statements printed to PDF are not password protected — the browser's own
  Save-as-PDF has no way to set one.
- No fingerprint unlock, receipt photos, or interest calculation yet.

## Storage

The ledger is kept in IndexedDB, which browsers allow hundreds of megabytes
(around 950 MB when measured on Chrome) rather than localStorage's ~5 MB.
At roughly 218 bytes per entry that is millions of entries — not a limit
normal use will reach. 60,000 entries write in about 100 ms.

Data written by earlier versions in localStorage is migrated to IndexedDB
automatically on first run, and the old copy is removed only once the new one
is safely written. If IndexedDB is unavailable, the app falls back to
localStorage and its smaller limit.

## Changing things

Colours live at the top of `index.html` in `:root`. Categories are the
`CATS` array near the top of the script. After changing any file, bump
`CACHE` in `sw.js` so phones fetch the new version.
