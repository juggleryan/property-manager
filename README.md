# 🏠 Property Manager

A self-hosted property management dashboard for tracking RV sites and rental homes across your portfolio. Runs entirely in the browser — no server required. Hosted free on GitHub Pages with optional Google Sheets sync and Zelle email detection.

---

## Properties

| Name | Address | Type |
|---|---|---|
| 166th Spanaway — Site 1 & 2 | 166th St, Spanaway WA | RV site |
| 101st Tacoma — Site 1 & 2 | 101st St S, Tacoma WA | RV site |
| Bethel House | 138 176th St E, Spanaway WA | House |
| Starbucks House | 416 166th St S, Spanaway WA | House |
| Casa Grande Home | 8932 S Chuichu Rd, Casa Grande AZ | House |

---

## Features

- **Dashboard metrics** — collected rent, outstanding balance, late fees, and occupancy updated in real time
- **Property cards** — tenant info, rent, paid status, lease dates, and notes per property
- **Filter tabs** — view all properties, RV sites only, houses only, late/overdue, Washington, or Arizona
- **Payment log** — manually log rent, late fees, deposits, and other payments with date and method
- **Mark paid / late fee** — one-click buttons per property card
- **Zelle email detection** — scans your Gmail inbox for Zelle confirmation emails and auto-logs payments
- **Google Sheets sync** — reads and writes all tenant and payment data to your own Google Sheet
- **Lease documents** — upload and assign lease PDFs per property, download the current lease
- **CSV export** — download all payment history as a spreadsheet
- **Demo mode** — works without any Google account for testing

---

## Setup

### 1. Deploy to GitHub Pages

1. Fork or upload this repo to your GitHub account
2. Rename `rv-manager.html` to `index.html` if you haven't already
3. Go to **Settings → Pages**
4. Under "Source", select `main` branch and click **Save**
5. Your dashboard will be live at `https://YOUR-USERNAME.github.io/property-manager`

> **Note:** GitHub Pages requires a **public repository** on the free plan. To keep the repo private, upgrade to GitHub Pro ($4/mo) or use Netlify's free tier instead.

---

### 2. Google Cloud Setup (for Sheets sync + Zelle detection)

You need a free Google Cloud project to connect Sheets and Gmail.

#### Create a project

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Click **Select a project → New Project**
3. Name it something like `property-manager` and click **Create**

#### Enable APIs

1. Go to **APIs & Services → Library**
2. Search for and enable **Google Sheets API**
3. Search for and enable **Gmail API**

#### Create an API Key

1. Go to **APIs & Services → Credentials**
2. Click **Create Credentials → API Key**
3. Copy the key — you'll paste it into the app's Setup screen
4. Optionally restrict it to Sheets and Gmail APIs for security

#### Create an OAuth 2.0 Client ID

1. Go to **APIs & Services → Credentials**
2. Click **Create Credentials → OAuth 2.0 Client ID**
3. Set application type to **Web application**
4. Under **Authorized JavaScript origins**, add your GitHub Pages URL:
   ```
   https://YOUR-USERNAME.github.io
   ```
5. Click **Create** and copy the Client ID

#### Configure the OAuth consent screen (first time only)

1. Go to **APIs & Services → OAuth consent screen**
2. Choose **External** and click **Create**
3. Fill in the app name (e.g. `Property Manager`) and your email
4. Add your email as a test user under **Test users**
5. Save and continue through the remaining steps

---

### 3. Google Sheet Setup

1. Go to [sheets.google.com](https://sheets.google.com) and create a new blank spreadsheet
2. Create three tabs named **exactly**:
   - `Sites`
   - `Payments`
   - `Leases`
3. Copy the Sheet ID from the URL — it's the long string between `/d/` and `/edit`:
   ```
   https://docs.google.com/spreadsheets/d/THIS-IS-YOUR-SHEET-ID/edit
   ```

#### Sheet column structure

**Sites tab** (row 2 onward):

| A | B | C | D | E | F | G | H | I | J | K | L | M | N |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| id | name | group | addr | type | state | tenant | rent | paid | start | end | status | lease | notes |

**Payments tab** (row 2 onward):

| A | B | C | D | E | F | G |
|---|---|---|---|---|---|---|
| date | site | tenant | type | amount | method | source |

---

### 4. Connect the app

1. Open your dashboard at your GitHub Pages URL
2. Enter your **OAuth Client ID**, **API Key**, and **Sheet ID**
3. Click **Connect to Google Sheets**
4. Sign in with your Google account when prompted
5. The app will load your sheet data and show "Sheets connected" in the top bar

---

## Zelle Email Detection

The **⚡ Check Zelle** button scans your Gmail for payment confirmation emails from your bank and auto-logs any payments it finds.

### How it works

1. Searches Gmail for emails matching your configured query (default: `subject:"You received money"`)
2. Parses the dollar amount from the email subject line
3. Tries to match the sender's name to a tenant in your Sites list
4. Logs the payment automatically with source marked as `Zelle Auto`
5. Skips any payments already logged (duplicate detection by date + amount)

### Configuring for your bank

Different banks format their Zelle emails differently. Open **⚙ Setup** and update the Gmail search query to match your bank:

| Bank | Suggested query |
|---|---|
| Chase | `subject:"You received money" from:chase.com` |
| Wells Fargo | `subject:"You received" from:wellsfargo.com` |
| Bank of America | `subject:"You received" from:bankofamerica.com` |
| BECU | `subject:"Zelle" from:becu.org` |

You can also test your query directly in Gmail to make sure it returns the right emails before connecting.

### Tenant name matching

The app matches payments to tenants by looking for the tenant's name in the email subject. Make sure tenant names in the app match how they appear in your Zelle contacts. If a match can't be found, the payment is logged under "Unknown" and you can correct it manually.

---

## Data & Privacy

- All data is stored in **your own Google Sheet** — nothing is sent to any third-party server
- Google credentials (API key, Client ID, Sheet ID) are stored in your browser's `localStorage` only
- The app runs entirely client-side — there is no backend
- If the repo is public, anyone can view the source code but cannot access your data without your Google credentials

---

## Local Development

You can run the app locally without GitHub Pages:

```bash
# Clone your repo
git clone https://github.com/YOUR-USERNAME/property-manager.git
cd property-manager

# Serve it locally (Python 3)
python3 -m http.server 8080

# Open in browser
open http://localhost:8080
```

For Google OAuth to work locally, add `http://localhost:8080` to your Authorized JavaScript origins in the Google Cloud Console.

---

## Updating the App

To push changes to GitHub Pages:

1. Edit `index.html` locally
2. Commit and push to the `main` branch:
   ```bash
   git add index.html
   git commit -m "Update dashboard"
   git push
   ```
3. GitHub Pages usually updates within 1–2 minutes

---

## Roadmap

- [ ] Google Drive integration for actual lease file storage and download
- [ ] Automated rent reminder emails via Gmail API
- [ ] Month-over-month income chart
- [ ] Lease expiration alerts
- [ ] Multi-user access with shared Google Sheet

---

*Built with vanilla HTML/CSS/JS. No frameworks, no dependencies, no build step.*# property-manager
