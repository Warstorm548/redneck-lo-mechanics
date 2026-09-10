# RedNeck Lo Mechanics — NFC business card page

A single-page, static site for **RedNeck Lo Mechanics** (James & Misty Combs).
It is the landing page written to the shop's NFC business card tags: tap the
tag, the phone opens this page, and the visitor can call either number or save
the contact.

**Live URL:** https://warstorm548.github.io/redneck-lo-mechanics/

## What's in the repo

| File | Purpose |
| --- | --- |
| `index.html` | The whole page. Self-contained: the card artwork is embedded as a base64 JPEG, with invisible tappable `tel:` links positioned over the printed phone numbers, two call buttons, and a **Save Contact** button. |
| `contact.vcf` | vCard 3.0 with an embedded photo. CRLF line endings, which iOS requires. |
| `.gitattributes` | Marks `*.vcf` as binary so Git never rewrites those CRLF endings. |
| `.nojekyll` | Tells GitHub Pages to serve the files as-is instead of running Jekyll. |
| `LICENSE` | AGPL-3.0. |

Two details are deliberate and should not be "fixed":

- The **Save Contact** link has **no `download` attribute**. Without it, iPhone
  Safari opens the native *Add Contact* sheet. With it, Safari saves the file
  to Files instead, which is worse.
- `contact.vcf` uses **CRLF** line endings, per the vCard spec. LF-only files
  are rejected or mangled by some phones.

## Changing a phone number

The numbers live in two files and in the card artwork. All three must agree.

### 1. `index.html`

Current numbers: 1st Phone `+1 (231) 383-3786`, 2nd Phone `+1 (231) 383-5325`.
Each appears in `tel:` form (`+12313833786`, digits only, no punctuation) and in
display form (`(231) 383-3786`). Lines as of this writing:

| Line | What it holds |
| --- | --- |
| 118 | `alt` text on the card image — both numbers, display form |
| 119 | 1st Phone hotspot over the artwork — `tel:` link and `aria-label` |
| 120 | 2nd Phone hotspot over the artwork — `tel:` link and `aria-label` |
| 124 | 1st Phone call button — `tel:` link |
| 126 | 1st Phone call button — visible display number |
| 128 | 2nd Phone call button — `tel:` link |
| 130 | 2nd Phone call button — visible display number |

If the line numbers have drifted, search the file for `383-3786` and
`12313833786` (and the same pair for the second number). Every hit must be
updated. Do not change anything else in `index.html`.

### 2. `contact.vcf`

| Line | Content |
| --- | --- |
| 7 | `item1.TEL;TYPE=CELL,VOICE,pref:+12313833786` — 1st Phone |
| 9 | `item2.TEL;TYPE=CELL,VOICE:+12313835325` — 2nd Phone |

Edit only the number after the final colon, in `+1XXXXXXXXXX` form. Leave the
`item1.` / `item2.` prefixes and the `X-ABLabel` lines on 8 and 10 alone — those
are what make the phone show "1st Phone" and "2nd Phone".

**Use an editor that preserves CRLF line endings** (VS Code: the status bar
shows `CRLF`; keep it there). Do not reflow or re-wrap the long `PHOTO:` line
starting on line 12 — its continuation lines begin with a single space and that
folding is part of the format.

### 3. The card artwork

The printed numbers are baked into the base64 JPEG on line 117 of `index.html`.
Changing a number means re-exporting the card image and replacing that data URI,
otherwise the picture and the links disagree.

After any change, open the page on an actual iPhone and an actual Android phone.
Tap both numbers, both call buttons, and Save Contact.

## Turning on GitHub Pages

1. Go to the repository's **Settings** tab.
2. Pick **Pages** in the left sidebar.
3. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
4. Set the branch to **main** and the folder to **/ (root)**.
5. Click **Save**.

The first build takes a minute or two. The Pages panel then shows the live URL.

## Transferring ownership

The repo is expected to move to a different GitHub account. **This changes the
live URL, and NFC tags do not update themselves.** Plan for it.

### Transferring the repository

1. In the repository's **Settings**, scroll to the **Danger Zone** at the bottom.
2. Click **Transfer** (Transfer ownership).
3. Enter the new owner's GitHub username or organization, type the repository
   name to confirm, and complete the transfer.
4. The new owner accepts the transfer from their email or notifications.

### The Pages URL changes and is not redirected

GitHub **does not redirect the old GitHub Pages URL after a transfer.**
`https://warstorm548.github.io/redneck-lo-mechanics/` will stop working. The
site's new address becomes:

```
https://NEWUSER.github.io/redneck-lo-mechanics/
```

where `NEWUSER` is the new owner's GitHub username, lowercased. If the new owner
also renames the repository, the last path segment changes to match.

### New owner's checklist

1. **Confirm Pages is still on.** Open **Settings > Pages** and check that the
   source is still **Deploy from a branch**, branch **main**, folder
   **/ (root)**. Re-enable it if the transfer turned it off.
2. **Load the new URL on a phone** and test both call links and Save Contact.
3. **Rewrite every NFC tag with the new URL.** Any tag still holding the old
   address is dead. Use NFC Tools (iOS/Android) or a similar writer, write the
   new URL as a URI/URL record, and re-test each tag by tapping it. Include any
   printed QR codes that point at the old address.

Rewriting the tags is the step people forget. Do it before handing cards out.
