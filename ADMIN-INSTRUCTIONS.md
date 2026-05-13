# Island Mind Lab — Admin Guide

A friendly walkthrough for managing your Caribbean-themed landing page.

---

## 1. Files in this package

- **`index.html`** — the entire landing page in one file. Everything (style, code, layout) lives here.
- **`ADMIN-INSTRUCTIONS.md`** — this guide.

That's it. One HTML file to host, one guide to keep.

---

## 2. How to host the site (pick any one)

The page is a single static HTML file. Any static host works. Recommended free options:

**Netlify (drag-and-drop, easiest):**
1. Go to https://app.netlify.com/drop
2. Drag `index.html` onto the page.
3. You get a live URL instantly. Rename the site in Site settings if you like.

**Vercel:**
1. Create a free account at https://vercel.com
2. New Project → import a folder containing `index.html`.

**GitHub Pages:**
1. Create a public repo, add `index.html`.
2. Settings → Pages → Source: `main` branch, `/ (root)`.

**Cloudflare Pages, Render, Surge.sh** — all work the same way.

> Tip: After the first upload, you only need to re-upload `index.html` whenever you edit content.

---

## 3. Where to edit your content

Open `index.html` in any text editor (VS Code, Notepad, TextEdit, Sublime).
Search for this line:

```html
<!-- SITE CONFIG — EDIT THIS BLOCK TO CHANGE YOUR CONTENT -->
```

Everything you'll ever need to change is in the `<script id="site-config">` block below it.

---

## 4. Updating the hero / branding

Inside `SITE = { ... }`:

```js
const SITE = {
  name:       "Island Mind Lab",            // shown in footer & browser tab
  tagline:    "🇯🇲 Welcome to the Island",  // small chip at top of hero
  heroScript: "Island Mind Lab",            // the script-style accent text
  heroTitle:  "Apps, Docs & Stories...",    // the big hero title
  heroLead:   "A curated home for...",      // the paragraph under the title
  aboutText:  "Island Mind Lab is where..." // the About section paragraph
};
```

Change any value between the quotes and save.

---

## 5. Adding, changing, or removing the Video of the Week

Inside `VIDEO_OF_WEEK = { ... }`:

```js
const VIDEO_OF_WEEK = {
  enabled:     true,
  title:       "Video of the Week",
  description: "This week's featured reel — click play to watch.",
  type:        "youtube",        // "youtube" | "vimeo" | "file"
  id:          "OLQok77eGwM",    // YouTube video ID
  poster:      ""                // leave "" to auto-use YouTube thumbnail
};
```

### Change the video
Replace the `id` value. Where to find the ID:
- **YouTube:** `https://youtu.be/ABC123xyz` → `id` is `ABC123xyz`. Or from `https://www.youtube.com/watch?v=ABC123xyz` → `id` is `ABC123xyz`.
- **Vimeo:** `https://vimeo.com/123456789` → `id` is `123456789` (and set `type: "vimeo"`).
- **File (.mp4):** Set `type: "file"` and put the direct file URL in `id`.

Update `title` and `description` to match the new video, then save.

### Remove the video (hide the section entirely)
Two ways:

**Option A — flip the switch:**
```js
const VIDEO_OF_WEEK = {
  enabled: false,
  // ...rest can stay as-is
};
```

**Option B — set the whole thing to null:**
```js
const VIDEO_OF_WEEK = null;
```

Either way, the Video of the Week section disappears from the page.

### Add the video back
Set `enabled: true` again (or restore the object) and update the `id`.

### Poster image
- For YouTube, leave `poster: ""` — the YouTube thumbnail loads automatically (HD if available, with a clean fallback).
- Otherwise, paste a public image URL.

---

## 6. Adding / editing / removing apps

Inside `APPS = [ ... ]`, each app is one block:

```js
{
  name:        "Reef Reader",
  description: "An interactive marine-life learning app...",
  image:       "https://images.unsplash.com/photo-...",   // card thumbnail
  url:         "https://example.com/reef-reader",          // hidden link
  ribbon:      "New"                                       // optional small badge
}
```

**To add a new app:** copy any block (including the trailing comma) and paste it inside the `[ ... ]` brackets. Edit the fields.

**To remove an app:** delete its entire `{ ... },` block.

**Image tips:**
- 900×600 (3:2) or 16:10 ratio works best.
- Free image sources: Unsplash, Pexels, your own uploads. If you upload images, put them in the same folder as `index.html` and use a relative URL like `images/reef-reader.jpg`.

**`ribbon`** is optional — remove the line or leave it empty if you don't want a badge.

---

## 7. Adding / editing PDFs

Inside `PDFS = [ ... ]`:

```js
{
  name:        "AI in Caribbean Education — Field Notes",
  description: "A short paper on emerging AI use...",
  url:         "https://yourhost.com/papers/ai-caribbean.pdf"
}
```

**Where to host PDFs:**
- The PDF must be on a host that allows cross-origin reads. Good options:
  - **Same host as your site** (drop the `.pdf` next to `index.html` and use a relative URL like `docs/my-paper.pdf`). **This is the most reliable option.**
  - **Netlify / Vercel / GitHub Pages** — works out of the box.
  - **AWS S3 / Cloudflare R2** — works if CORS is enabled (public read).
- **Avoid:** Google Drive shareable links, Dropbox shared links — these usually block embedding.

**No-download behavior:**
- PDFs open inside a custom secure viewer.
- The browser's download / print / right-click are blocked.
- ⚠️ Heads-up: no web viewer can be 100% download-proof. A determined visitor with developer tools can still capture the file. The setup here stops casual saving, which covers the typical visitor.

---

## 8. The email gate (how it works)

- The first time a visitor clicks any app, PDF, or video, they're asked for their **name and email**.
- They can tick the "save on this device" box to skip the gate on future visits.
- Emails are stored **locally in their browser** (`localStorage`). They are **not sent anywhere** unless you wire it up.

**Want to actually collect emails to your inbox / spreadsheet?**
Easy upgrade — replace the `saveGate(...)` function inside `index.html` with one of these:

### Option A — Formspree (free tier, email-to-inbox)
1. Sign up at https://formspree.io and create a form. You'll get an endpoint like `https://formspree.io/f/abcd1234`.
2. Find `function saveGate(name, email, consent)` in `index.html`.
3. Add this inside that function (before the `localStorage.setItem` line):

```js
fetch("https://formspree.io/f/abcd1234", {
  method: "POST",
  headers: { "Accept": "application/json", "Content-Type": "application/json" },
  body: JSON.stringify({ name, email, source: "Island Mind Lab landing" })
}).catch(() => {});
```

### Option B — Google Sheets via Apps Script
1. Make a Google Sheet, then Extensions → Apps Script.
2. Paste a small script that accepts a POST and appends a row. Deploy it as a Web App ("anyone with the link").
3. Use the deployment URL in the same `fetch(...)` snippet above.

### Option C — Netlify Forms (if hosting on Netlify)
Add `data-netlify="true"` to the `<form id="gate-form">` element. Netlify auto-collects submissions.

---

## 9. Changing the Caribbean theme colours

At the top of the `<style>` block in `index.html`:

```css
:root {
  --turquoise: #00BFA6;
  --coral:     #FF6B6B;
  --sunset:    #FF8C42;
  --gold:      #FFB627;
  --palm:      #2D8659;
  --sand:      #FFF5E1;
  --navy:      #1A3A5C;
}
```

Change any colour code and the whole site updates. Use https://coolors.co/ to play with palettes.

---

## 10. Resetting your own access while testing

There's a small **"Reset access"** button at the very bottom of the page. Click it to clear your saved email and see the gate again. Useful when previewing.

---

## 11. Quick checklist before publishing

- [ ] All app images load (check each card)
- [ ] All app links open the right URL when clicked
- [ ] PDF previews render (the first page should appear on each card)
- [ ] PDF viewer opens and you can flip pages
- [ ] Video of the Week plays after entering email
- [ ] Site looks good on mobile (resize your browser window)
- [ ] Tagline / hero text reflects your branding
- [ ] (Optional) Email-capture wired up to Formspree or similar

---

## 12. Need a tweak?

Common requests, easy fixes — bring them back to me and I'll update the file:
- Add a Donate / Subscribe button
- Add a contact form
- Add categories or filters to the apps grid
- Switch to a different palette (Jamaican Pride, Reggae Earth, etc.)
- Add multilingual support (English + Patois, French Creole, Spanish, etc.)

🌴 One love, one island, one curious mind.
