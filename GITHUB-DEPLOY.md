# Deploying to GitHub Pages

A clean, free, fast way to host your landing page. End-to-end: ~10 minutes.

---

## Step 1 — Create the repository

1. Go to https://github.com/new
2. **Repository name:** `caribbean-landing` (or anything you like, no spaces).
3. **Visibility:** Public (required for free GitHub Pages on personal accounts).
4. Leave everything else default.
5. Click **Create repository**.

You'll see a page that looks like *"Quick setup — if you've done this kind of thing before"*. Leave it for a moment.

---

## Step 2 — Upload the files

Easiest path (no command line needed):

1. On the repo page, click the link **"uploading an existing file"** (about a third of the way down).
2. Drag and drop these files into the upload area:
   - `index.html`
   - `admin.html`
   - `README.md`
   - `GITHUB-DEPLOY.md`
   - `GOOGLE-SHEETS-SETUP.md`
   - `ADMIN-INSTRUCTIONS.md`
   - `.gitignore`
3. Scroll down. In the **Commit changes** box, type "Initial site upload".
4. Click **Commit changes**.

You'll now see all your files in the repository's main view.

---

## Step 3 — Turn on GitHub Pages

1. In the repository, click **Settings** (top tab, far right).
2. In the left sidebar, click **Pages**.
3. Under **Build and deployment**:
   - **Source:** `Deploy from a branch`.
   - **Branch:** `main` and `/ (root)`.
4. Click **Save**.

Wait ~1–2 minutes. Refresh the Pages screen — you'll see a green box with your live URL, e.g.:

```
https://<your-github-username>.github.io/caribbean-landing/
```

That's your live landing page. Bookmark it.

The admin dashboard lives at:

```
https://<your-github-username>.github.io/caribbean-landing/admin.html
```

---

## Step 4 — Change the admin password (do this now)

1. Open `https://.../admin.html` and log in with the default password `rohan2026`.
2. Go to the **Settings** tab.
3. Enter your new password twice, click **Generate hash**.
4. Copy the line that appears (it starts with `const PASSWORD_HASH = ...`).
5. Back on GitHub, open `admin.html` in the repo, click ✏️ Edit, find the existing `const PASSWORD_HASH` line near the top, replace it with your new line, scroll down, **Commit changes**.
6. Refresh `admin.html` and log in with the new password to confirm.

---

## Step 5 — Wire up email tracking (optional but recommended)

Follow `GOOGLE-SHEETS-SETUP.md`. You'll end up pasting a single URL into the Site tab of admin.html, then regenerating + committing.

---

## Updating content later (the everyday flow)

1. Open `https://.../admin.html`. Log in.
2. Edit Site / Video / Apps / PDFs.
3. Open the **Generate** tab → click **Generate** → **Copy config**.
4. In GitHub, open `index.html` → ✏️ Edit.
5. Use **Ctrl+F (Cmd+F on Mac)** to find `<!-- CONFIG-START -->`.
6. Select everything from that line through `<!-- CONFIG-END -->` (inclusive).
7. Paste the copied content over it.
8. Scroll down → **Commit changes**.

GitHub Pages will redeploy in 30–60 seconds.

---

## Custom domain (optional)

If you own a domain like `rohanjowallah.com`:

1. In repo **Settings → Pages → Custom domain**, enter `www.yourdomain.com`. Save.
2. At your DNS provider, add a CNAME record pointing `www` to `<your-username>.github.io`.
3. Wait 5–30 minutes for DNS to propagate.
4. Back on Pages, check **Enforce HTTPS**.

Your site will be live at `https://www.yourdomain.com`.

---

## Updating files in bulk (optional command-line path)

If you're comfortable with `git`:

```bash
git clone https://github.com/<you>/caribbean-landing.git
cd caribbean-landing
# edit files locally
git add .
git commit -m "Update content"
git push
```

GitHub Pages redeploys automatically.

---

## Common questions

**Q: Why does the admin password live in admin.html (anyone can read it)?**
A: GitHub Pages serves only static files — no server-side secrets. The password is stored as a SHA-256 hash, which means visitors can't reverse it, but they *can* know that one exists. This is "good enough" for low-stakes personal sites. For higher security, host the admin on a service like Netlify Identity, Cloudflare Access, or a backend.

**Q: Can I make my repo private and still use GitHub Pages?**
A: Only on paid plans (GitHub Pro/Team). On the free plan, GitHub Pages requires a public repo.

**Q: How do I add a PDF?**
A: Drop the `.pdf` into your repo (alongside `index.html`). Open admin → PDFs tab → Add PDF → URL: `your.pdf` (relative path works because it's on the same domain).
