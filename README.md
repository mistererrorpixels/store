# Error Pixels — Premium Assets for Creators

A lightweight storefront for selling digital assets (LUTs, Presets, Overlays, Project Files) with PayU payment integration and Google Apps Script backend.

---

## Project Structure

```
error-pixels/
├── public/
│   └── index.html          # Frontend — single-page app
├── gas/
│   └── Code.gs             # Google Apps Script backend
├── vercel.json             # Vercel deployment config
└── README.md
```

---

## Setup Guide

### 1. Google Sheets

Create a spreadsheet with **three sheets**:

**Assets** (columns A–H):
| A: id | B: name | C: category | D: description | E: price | F: driveFileId | G: thumbnail | H: active |

**Orders** (columns A–K):
| A: timestamp | B: txnid | C: assetId | D: assetName | E: buyerName | F: email | G: phone | H: amount | I: status | J: mihpayid | K: downloadSent |

**Logs** (columns A–C):
| A: timestamp | B: action | C: details |

---

### 2. Google Apps Script

1. Open your Google Sheet → **Extensions → Apps Script**
2. Paste the contents of `gas/Code.gs`
3. Set the following **Script Properties** (Project Settings → Script Properties):

| Key | Value |
|-----|-------|
| `SHEET_ID` | Your Google Sheet ID (from URL) |
| `PAYU_KEY` | Your PayU merchant key |
| `PAYU_SALT` | Your PayU salt |
| `PAYU_URL` | `https://secure.payu.in/_payment` (prod) or `https://test.payu.in/_payment` (test) |
| `SUCCESS_URL` | `https://your-domain.vercel.app/?status=success` |
| `FAILURE_URL` | `https://your-domain.vercel.app/?status=failure` |
| `ADMIN_EMAIL` | Your email for order notifications |

4. **Deploy → New Deployment → Web App**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Copy the Web App URL

---

### 3. Frontend

Open `public/index.html` and replace:

```js
const APPS_SCRIPT_URL = "YOUR_APPS_SCRIPT_WEB_APP_URL_HERE";
```

with your actual Apps Script Web App URL.

---

### 4. Deploy to Vercel via GitHub

1. Push this repo to GitHub
2. Go to [vercel.com](https://vercel.com) → **Add New Project**
3. Import your GitHub repo
4. Vercel auto-detects `vercel.json` — no extra config needed
5. Click **Deploy**

Your site will be live at `https://your-project.vercel.app`

---

## PayU Webhook

Set your **PayU dashboard → Webhook URL** to your Apps Script Web App URL. PayU will POST payment results to it automatically.

---

## Notes

- All payment hash signing is done server-side in GAS (never expose your salt in the frontend)
- Download links are Google Drive direct-download URLs, valid only after `SUCCESS` status is confirmed
- The `Logs` sheet captures all webhook events and errors for debugging
