# 🩸 CME Department Blood Bank Website

**GitHub Pages + Google Sheets + Apps Script** — completely free, no server, no database cost.

---

## 📁 What's in this folder

| File | Purpose |
|---|---|
| `index.html` | Main website (dashboard, donor search, blood info, compatibility checker, facts & FAQ, dark mode, installable PWA) |
| `CME_donors_clean.csv` | **Merged donor data** (231 donors, 4 series) — ready to upload to Google Sheets |
| `script.gs` | Apps Script code for Google Sheets backend |
| `manifest.json` | PWA app manifest (name, icons, colors) — needed for app install |
| `sw.js` | Service worker — makes the site work offline + installable |
| `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png` | App icons (phone home screen / laptop) |
| `serve.py` | Local test server (no-cache) — run `python serve.py`, open http://localhost:8899 |
| `start_site.bat` | Double-click to start the local server and open the site |

---

## 🗂️ Your Data (Verified)

**Merged from 4 Excel files → 231 donors:**

| Series | Donors | Roll Range |
|---|---|---|
| 2021-22 | 59 | 2106001–2106060 |
| 2022-23 | 54 | 2206001–2206060 |
| 2023-24 | 59 | 2306001–2306060 |
| 2024-25 | 59 | 2406001–2406060 |
| **Total** | **231** | |

**Data checks performed:**
- ✅ All roll formats validated (0 mismatches)
- ✅ No duplicate rolls across files
- ✅ No missing names/blood groups
- ✅ 2306055 (2023-24), 2406022 (2024-25), 2106045 (2021-22) and several 2022-23 rolls had no form entry — these were never in your source files, **not** a data error
- ✅ Blood groups: B+ 81, O+ 68, A+ 50, AB+ 26, B- 4, O- 2

---

## 🚀 Setup Steps (Do these in order)

### Step 1: Create the Google Sheet

1. Go to [sheets.new](https://sheets.new) (or Google Drive → New → Google Sheets)
2. You'll get a blank spreadsheet
3. Rename sheet tab `Sheet1` → **`Donors`**
4. Row 1 (header): `Name`, `Roll`, `Series`, `Blood Group`
5. **Copy everything from `CME_donors_clean.csv` (starting from row 2)** and paste into the sheet
   - You can open the CSV in any text editor, select all from line 2, copy, then paste into cell A2
   - Or use: File → Import → Upload → `CME_donors_clean.csv` → "Replace spreadsheet"

**Verify:** Sheet should show 231 donor rows + 1 header row.

---

### Step 2: Deploy the Apps Script Backend

1. In your Google Sheet: **Extensions → Apps Script**
2. The script editor opens — **delete everything in `Code.gs`**
3. **Copy the entire content of `script.gs`** and paste into the editor
4. Click the **Save** icon (💾)
5. Click **Deploy** → **Manage deployments**
6. Click the **Add deployment** (⚙️) button → **New deployment**
7. Click **Select type** → choose **Web app**
8. Fill in:
   - **Description**: `CME Blood Bank API`
   - **Execute as**: `Me` (your Gmail)
   - **Who has access**: `Anyone` ← **Important!** (otherwise the website can't read data)
9. Click **Deploy**
10. **Authorize**: Google will ask for permission → click "Advanced" → "Go to CME Blood Bank (unsafe)" → "Allow"
11. **Copy the Web App URL** — it looks like:
    ```
    https://script.google.com/macros/s/AKfycb.../exec
    ```
    **Save this URL** — you need it in Step 3!

---

### Step 3: Connect the Website to the Database

1. Open `index.html` in any text editor (Notepad, VS Code, etc.)
2. Find this line (around line 1194):
   ```javascript
   let dataUrl = 'DATA_URL_PLACEHOLDER';
   ```
3. **Replace `DATA_URL_PLACEHOLDER` with your actual Web App URL** (the one you copied in Step 2):
   ```javascript
   const dataUrl = 'https://script.google.com/macros/s/AKfycb.../exec';
   ```
4. **Save** the file

---

### Step 4: Publish to GitHub Pages

**Option A — If you already have a GitHub account & repo:**

1. Create a new repo on GitHub (e.g., `cme-blood-bank`)
2. Copy **ALL website files** into the repo — not just index.html:
   `index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png`, `CME_donors_clean.csv`
   (Keeping them together is required — the icons, manifest and service worker make the "Install App" feature work)
3. Go to repo **Settings → Pages**
4. Under "Source", choose **Deploy from a branch** → **main** (or master) branch → **Save**
5. Your site will be live at: `https://<your-username>.github.io/<repo-name>/`

**Option B — Easiest (no GitHub account needed, just for testing):**

Use a free static host like [Netlify Drop](https://app.netlify.com/drop):
1. Put ALL website files (same list as above) in a folder
2. Drag & drop that folder onto Netlify Drop
3. Your site is live in seconds

---

## 📲 Install as an App (PWA)

Once the site is online (or on localhost during testing), it can be installed like a real app:

**On laptop / PC (Chrome or Edge):**
1. Open the site → look for the **install icon** (⊕ / monitor icon) in the address bar, OR click **"📲 Install App"** in the site's sidebar
2. Click **Install** → the app opens in its own window with an icon in your apps list

**On Android phone (Chrome):**
1. Open the site → tap **⋮ menu** → **"Install app"** / **"Add to Home screen"**
2. The app icon appears on the home screen and opens fullscreen

**On iPhone (Safari):**
1. Open the site in Safari → tap **Share** (⬆️) → **"Add to Home Screen"**
2. Tap **Add** — the icon appears on the home screen

**Notes:**
- Install fully works on **https://** sites (GitHub Pages) and on **localhost**. Testing from a phone via a local IP address (http://192.168.x.x) shows only a basic shortcut — install properly after deploying to GitHub Pages.
- Once installed, the site opens offline too (a cached copy is kept by the service worker).

---

## 🎯 Features (as requested)

1. **Dashboard layout** — sidebar navigation, stat cards (total donors / series / blood groups), donor statistics by blood group
2. **Donor Directory Search** — Blood Group + Series dropdowns, click Search → shows **all matching donors at once** (Name + Roll + Series + Blood Group), with a **"📋 Copy All"** button to copy the full result list
3. **Blood Group Information** — 8 groups display, donation matrix, receiving matrix
4. **Compatibility Checker** — Donor + Patient selector → "Compatible ✓ / Not Compatible ✗"
5. **Facts & FAQ (Bangladesh)** — WHO/BTSB-based content, corrected local donation interval (4 months) and volume (350–450 mL)
6. **Dark mode toggle** + mobile-responsive design + **installable as an app (PWA)**
7. **CME Department branding** — sidebar, header, footer

---

## 📝 How to Add New Donors Later

No coding needed! Just add a new row in the Google Sheet:

1. Open your Google Sheet
2. Row 232 (right after the last donor): `Rahim Ahmed | 2506001 | 2025-26 | O+`
3. **The website updates automatically** within a minute (Apps Script caches for 6 minutes)
4. New series auto-appear in the dropdown

---

## 🔧 Troubleshooting

| Problem | Fix |
|---|---|
| "0 donors found" even after search | Make sure Step 3 URL is correct in `index.html` and GitHub Pages is refreshed |
| Search button does nothing | Check browser console (F12) for CORS errors — means Sheet isn't public. Re-deploy with "Who has access: Anyone" |
| Dark mode doesn't save | Clear browser cache — theme is stored in localStorage |

---

## 📂 Next Steps You Might Want

- **Add a donation request form** (students fill → row added to Sheet)
- **Email notifications** when new donor adds
- **More series** (2025-26, 2026-27) as batches register

---

*Built with ❤️ for CME Department, RUET*
