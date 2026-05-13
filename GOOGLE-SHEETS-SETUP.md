# Email Tracking — Google Sheets Setup

This wires every email signup AND every contact-form message into a Google Sheet
you own. Free, unlimited, takes ~15 minutes.

You'll end up with two tabs in one Sheet:
- **Signups** — name, email, when, page, consent
- **Messages** — speaking-engagement and consulting messages with subject, message body, organisation, type

---

## Step 1 — Create the Sheet

1. Go to https://sheets.google.com → **Blank**.
2. Rename it: **Caribbean Landing — Captures**.
3. Rename the first tab to **Signups**.
4. In row 1 of **Signups**, type these headers (in this order, exact spelling):

   ```
   When | Name | Email | Consent | Source | Page | User Agent
   ```

5. Click the **+** at the bottom left to add a second tab. Rename it **Messages**.
6. In row 1 of **Messages**, type these headers:

   ```
   When | Type | Name | Email | Organisation | Subject | Message | Source | Page
   ```

7. Save. Keep this tab open.

---

## Step 2 — Open Apps Script

1. In your Sheet, click **Extensions** → **Apps Script**.
2. A new tab opens. Delete the default `function myFunction() {}` content.
3. Paste the entire script below.

```javascript
// =========================================================================
// Caribbean Landing — capture endpoint
// Receives JSON via POST from index.html and routes it to the right tab.
// =========================================================================

function doPost(e) {
  try {
    var data = {};
    try { data = JSON.parse(e.postData.contents); } catch (_) { data = e.parameter || {}; }

    var ss = SpreadsheetApp.getActiveSpreadsheet();
    var isContact = String(data.type || "").toLowerCase() === "contact";
    var sheet = isContact
      ? (ss.getSheetByName("Messages") || ss.insertSheet("Messages"))
      : (ss.getSheetByName("Signups")  || ss.insertSheet("Signups"));

    if (isContact) {
      sheet.appendRow([
        new Date(),
        data.kind     || "",
        data.name     || "",
        data.email    || "",
        data.org      || "",
        data.subject  || "",
        data.message  || "",
        data.source   || "",
        data.page     || ""
      ]);
    } else {
      sheet.appendRow([
        new Date(),
        data.name     || "",
        data.email    || "",
        data.consent ? "Yes" : "Session-only",
        data.source   || "",
        data.page     || "",
        data.userAgent || ""
      ]);
    }
    return ContentService
      .createTextOutput(JSON.stringify({ ok: true }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ ok: false, error: String(err) }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet() {
  return ContentService.createTextOutput("Caribbean Landing capture endpoint is live.");
}
```

4. Click the **floppy disk icon** to save (or press Cmd/Ctrl+S). Name the project "Caribbean Landing Capture".

---

## Step 3 — Deploy as a Web App

1. Click **Deploy** (top right) → **New deployment**.
2. Click the gear icon ⚙️ next to "Select type" → choose **Web app**.
3. Fill in:
   - **Description:** "Caribbean Landing"
   - **Execute as:** *Me* (your account)
   - **Who has access:** **Anyone**
4. Click **Deploy**.
5. Google will ask you to authorise the script:
   - Click **Authorize access** → choose your Google account.
   - You'll see "Google hasn't verified this app" → click **Advanced** → **Go to Caribbean Landing Capture (unsafe)** → **Allow**.
   - (This warning appears because you're the developer and the script isn't published publicly. It's safe — it's your own code.)
6. You'll be shown a **Web app URL** like:
   ```
   https://script.google.com/macros/s/AKfycbx.../exec
   ```
7. **Copy this URL**. You'll need it in the next step.

---

## Step 4 — Paste the URL into your landing page

1. Open `https://your-username.github.io/your-repo/admin.html`.
2. Log in.
3. Go to **Site** tab.
4. Paste the Web app URL into **Google Sheets endpoint URL**.
5. Click **💾 Save draft**.
6. Open the **Generate** tab → click **Generate** → **Copy config**.
7. On GitHub, open `index.html` → ✏️ Edit, find `<!-- CONFIG-START -->`, replace through `<!-- CONFIG-END -->`, commit.

Wait 30–60 seconds for GitHub Pages to redeploy.

---

## Step 5 — Test it

1. Open your live site in an **incognito / private window** (so the gate appears fresh).
2. Click any app → enter a test name & email → submit.
3. Open your Google Sheet → switch to the **Signups** tab — your test row should appear within a few seconds.
4. Go to the **Contact** section on your site → fill the form → submit.
5. Switch to the **Messages** tab in the Sheet — your test message should appear.

If both rows appear, everything works.

---

## Optional — Get an email when a new message arrives

In Apps Script, add this at the bottom of the file:

```javascript
function onContact(data) {
  if (!data || String(data.type || "").toLowerCase() !== "contact") return;
  var to = "you@example.com";  // <-- your email here
  MailApp.sendEmail({
    to: to,
    subject: "New inquiry: " + (data.subject || "(no subject)"),
    body: "From: " + (data.name || "") + " <" + (data.email || "") + ">\n" +
          "Organisation: " + (data.org || "") + "\n" +
          "Type: " + (data.kind || "") + "\n\n" +
          (data.message || "") + "\n\n— Sent via your landing page"
  });
}
```

Then change one line in `doPost`. Just after the `if (isContact)` row insertion, add:

```javascript
      onContact(data);
```

Re-deploy: **Deploy → Manage deployments → ✏️ → Version: New version → Deploy**.

You'll get an email every time a new message arrives.

---

## Updating the script later

If you change the Apps Script code, you must **redeploy** for changes to take effect:

1. **Deploy → Manage deployments**.
2. Click the ✏️ pencil next to your active deployment.
3. **Version:** select **New version**.
4. Click **Deploy**.

The same Web app URL keeps working — no need to update anything in your site.
