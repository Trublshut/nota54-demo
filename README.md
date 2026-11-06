# Nota54 live demo (static, three pages)

- `backend.html`: forwarder backend. Open it on the presenter's laptop or projector. It shows the QR code, processes inquiries and sends back quotes. Use **Open ops board** to launch the second laptop screen.
- `index.html`: customer page (Sample Forwarder Ltd, powered by Nota54). Investors open it on their phones by scanning the QR code.
- `ops.html`: forwarder Ops · orders board (second laptop screen). Shows accepted quotes as jobs **In progress** with a fake “Synced to forwarder DB” record id.

All data is sample data: carriers A–H are fictional and the rates are not real carrier rates. Extraction is rule-based; no paid AI is used.

## How it works
- The backend generates a random session code (16 characters) and shows a QR code for `index.html?s=<code>`.
- **Open ops board** on the backend opens `ops.html?c=<code>` in a new tab with the same session.
- Messages go through the free public relay ntfy.sh. No account or server of our own is needed.
  - The phone posts its inquiry to `nota54-demo-<code>-in`.
  - The backend replies on `nota54-demo-<code>-out-<inquiryId>` with an acknowledgement, the quote and the booking confirmation.
  - Accept / decline also go on the `-in` topic; the ops board listens there (and optionally hydrates from the backend’s localStorage when opened on the same browser).
- Nothing is computed on the phone. The backend tab does the extraction, validation, 8-carrier rates, 40/25/15/10/10 scoring, the quote and the approval gate.
- **Keep the backend tab open and the laptop awake during the demo.** Keep the ops board tab open on a second window or monitor.

## Three-page demo flow
1. Laptop A: open `backend.html` → Live · listening. Auto-approve on.
2. Laptop A (or B): click **Open ops board** → second screen joins the same session.
3. Phone: scan QR → send inquiry → watch quote arrive.
4. Phone: Accept quote → ops board row flips to **In progress** with `Synced to forwarder DB · SF-2026-XXXX`.
5. Backend continues to Booked; ops board follows when the booking confirmation is observed.

## Publish on GitHub Pages (free)
1. Create a public repo, e.g. `nota54-demo`, and upload `index.html`, `backend.html` and `ops.html` (this folder, without `tool/` and `shots/`).
2. Go to Settings → Pages → Source: "Deploy from a branch", choose `main` / root, then Save.
3. After about a minute the site is live at `https://<github-user>.github.io/nota54-demo/`.
   - Present `https://<github-user>.github.io/nota54-demo/backend.html`.
   - Ops board: `…/ops.html?c=<session>` (or use the button).
   - The QR code points to `index.html` in the same folder automatically.
4. Optional custom domain via Namecheap (e.g. `demo.trublshut.com`):
   - In Namecheap, add a CNAME record: host `demo` → `<github-user>.github.io`.
   - In GitHub, go to Settings → Pages → Custom domain, enter `demo.trublshut.com` and tick "Enforce HTTPS".

## Before presenting
- Open `backend.html` and check that it shows "Live · listening for inquiries".
- Click **Open ops board** and confirm the second screen shows "Live · listening for orders".
- Press "Simulate inquiry" once to warm up.
- Press "New session" to get a fresh QR code and an empty list (re-open ops board after a new session so it picks up the new code).
- "Auto-approve for demo" is on by default. Turn it off to show the manager approval step for quotes above USD 5,000.

## Limits (ntfy.sh free, anonymous)
- 250 messages per day per public IP address. Each inquiry uses about 5 messages: 2 from the phone, 3 from the laptop.
- Per-IP limits: about 60 requests in a burst, then about 1 every 5 seconds, and about 30 open subscriptions.
- The ops board adds one extra subscription on the session `-in` topic (and one per known inquiry on `-out-<id>`). Stay well under the ~30 open-subscription limit for typical demo sizes.
- If everyone is on the same venue Wi-Fi, they share one IP. Ask investors to use mobile data if the room is large (more than about 25 phones).
- Messages are cached for 12 hours. Topics are public to anyone who knows the random code, so the phone form says not to enter confidential information.
- ntfy.sh is a free community service with no uptime guarantee. If it is down, phones show "the team is reviewing your inquiry" after 45 seconds.
