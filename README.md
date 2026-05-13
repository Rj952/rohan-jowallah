# Caribbean AI & Education — Landing Page

A single-page portfolio site for original AI-powered Caribbean education tools.
Includes an email gate, in-page PDF viewer, video of the week, contact form,
flag strip of Caribbean nations, and a password-protected admin dashboard.

## Files

| File | Purpose |
|---|---|
| `index.html` | The public landing page. One file, no build step. |
| `admin.html` | Password-protected dashboard to edit content + view signups. |
| `README.md` | This file. |
| `GITHUB-DEPLOY.md` | Step-by-step: push to GitHub, turn on Pages. |
| `GOOGLE-SHEETS-SETUP.md` | Wire emails + contact messages into a Google Sheet. |
| `ADMIN-INSTRUCTIONS.md` | Day-to-day editing reference (content config). |
| `.gitignore` | Standard ignore rules. |

## Quick start

1. **Deploy** — follow `GITHUB-DEPLOY.md` to put the site live (≈ 10 minutes).
2. **Track signups** — follow `GOOGLE-SHEETS-SETUP.md` to capture emails and contact messages into a Sheet (≈ 15 minutes).
3. **Edit content** — open `your-site/admin.html`, log in, change content, click **Generate** and paste back to `index.html` via GitHub's web editor.

## What lives where

- **Hero, tagline, About copy** → `SITE` object in `index.html` (or the Site tab in admin).
- **Featured video** → `VIDEO_OF_WEEK` object.
- **Apps** → `APPS` array. Each app: name, description, image, hidden URL, optional ribbon.
- **PDFs** → `PDFS` array. Each PDF renders a live thumbnail and opens in a no-download viewer.
- **Contact submissions** → posted to your Google Apps Script Web App URL, with `type: "contact"`.
- **Signup submissions** → posted to the same URL, no `type` field (or `type: "signup"`).

## Default admin password

The default is `rohan2026`. **Change it immediately** via the Settings tab on
your first login. See `admin.html` — the line beginning with `const PASSWORD_HASH`.

## Copyright

© Rohan Jowallah. All apps and content original. All rights reserved.
