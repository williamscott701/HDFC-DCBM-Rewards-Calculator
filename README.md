# HDFC Credit Card Rewards Calculator

A browser-based rewards calculator for premium HDFC credit cards. No installs, no backend — just open the HTML file and start tracking.

**🔗 Live app: https://williamscott701.github.io/HDFC-DCBM-Rewards-Calculator/**

Also includes a detailed reference analysis for the **Axis Magnus Burgundy** card.

---

## Features

- **Multi-card support** — HDFC Diners Black Metal, Infinia Metal, Regalia / Regalia Gold, Diners Privilege
- **Accurate cap tracking** — combined SmartBuy daily/monthly caps, plus per-category caps (Voucher, Gyftr, Dining, Utility/Telecom/Grocery, Insurance), processed in chronological order
- **Accelerated rewards** — SmartBuy portal categories (Hotels, Flights, Trains, RedBus, IGP.com, Jockey, PharmEasy, Nykaa, Myntra, MMT Holiday, Voucher, Gyftr, Imagine, and more)
- **Excel import / export** — load transactions from `.xlsx` and export results via [SheetJS](https://sheetjs.com/)
- **Zero data leaves your device** — everything runs locally in the browser; no server, no tracking

---

## Usage

### Getting Started

**Option 1 — use it online:** open https://williamscott701.github.io/HDFC-DCBM-Rewards-Calculator/ — nothing to install, nothing leaves your browser.

**Option 2 — run it locally:**
1. Download or clone this repo
2. Open `index.html` (or `rewards-calculator.html`) in any modern browser
3. Select your HDFC card from the dropdown
4. Add transactions and view rewards in real-time

No build step or internet connection required (CDN assets load on first open and may be cached).

### Adding Transactions Manually

1. Select the transaction date
2. Choose a category
3. Optionally enter a description (defaults to a per-category placeholder, e.g. "Hotel Booking")
4. Enter **Total Charged** — the full amount that appears on your card statement
5. Optionally enter **Tax/Fees** — GST/convenience fees to exclude from the reward base (base amount = Total Charged − Tax/Fees)
6. Optionally enter an **Upfront Discount**
7. Click **Add Transaction**

### Importing from Excel

1. Click **Open File** (uses the File System Access API where supported, so the same file can be saved back in place) and select your `.xlsx` file
2. Required columns: `Date`, `Category`, `Amount` (amount is treated as the base amount for reward calculation)
3. Optional columns: `Use` (yes/no to include/exclude a row), `Description` (or `Merchant`), `Tax` (or `Tax Amount`), `Upfront Discount`
4. Rows missing `Date`, `Category`, or `Amount`, or with a non-positive amount, are skipped and counted in the import summary

### Exporting Data

- **Save** writes back to the currently open file (via the File System Access API) if one was opened with **Open File**
- **Download** saves a new timestamped Excel file named `<CardName>_Rewards_<date>.xlsx` with all transactions and calculated rewards

### Downloading a Template

Click **Template** to get a pre-formatted Excel file with sample rows and the correct categories for the selected card (or a generic template if no card is selected yet).

---

## Supported Cards

Base rewards accrue in whole-point blocks (e.g. 5 points per ₹150 spent, rounded down), not a flat percentage — the rates below are the effective base rate. The combined SmartBuy cap applies across *all* SmartBuy categories together (Hotels, IGP.com, Jockey, PharmEasy, Flights, RedBus, Voucher, Imagine, Nykaa, Myntra, MMT Holiday, Trains, Gyftr), not per category.

| Card | Base Rate | SmartBuy Combined Cap (Daily / Monthly) | Notable Multipliers |
|---|---|---|---|
| HDFC Diners Black Metal | 3.33% (5 RP/₹150) | ₹10,000 / ₹10,000 (₹75,000/statement cycle) | 10x Hotels/IGP.com/Jockey/PharmEasy, 5x Flights/RedBus/Nykaa/Myntra/MMT Holiday, 3x Voucher/Imagine/Trains/Gyftr, 2x Weekend Dining (₹1,000/day cap) |
| HDFC Infinia Metal | 3.33% (5 RP/₹150) | ₹15,000 / ₹15,000 | 10x Hotels/IGP.com/Jockey/PharmEasy, 5x Flights/RedBus/Imagine/Nykaa/Myntra/MMT Holiday, 3x Trains/Gyftr/Voucher (Voucher & Gyftr also have their own ₹5,000/day+month caps) |
| HDFC Regalia / Regalia Gold | 2.67% (4 RP/₹150) | ₹2,000 / ₹4,000 | 10x Hotels/IGP.com/Jockey/PharmEasy, 5x Flights/RedBus/Nykaa/Myntra/MMT Holiday/Imagine, 3x Voucher/Trains/Gyftr (Voucher & Gyftr have their own ₹2,000 day/month sub-caps) |
| HDFC Diners Privilege | 2.67% (4 RP/₹150) | ₹2,000 / ₹4,000 | Same category structure as Regalia |

All cards also earn 1x on Utility/Telecom (and Grocery, for Diners Black Metal) capped at ₹2,000/month combined, 1x on Insurance (capped, varies by card), and 1x uncapped on Others.

### Category Groups

Category names shown in the app reflect the SmartBuy portal directly rather than broad groupings:

| Group | Categories |
|---|---|
| SmartBuy Travel | Hotels, Flights, RedBus, Trains, MMT Holiday |
| SmartBuy Shopping | IGP.com, Jockey, PharmEasy, Nykaa, Myntra, Imagine |
| SmartBuy Vouchers | Voucher, Gyftr |
| Dining | Dining (Weekend) — Diners Black Metal only |
| Bills | Utility, Telecom, Grocery (Diners Black Metal only), Insurance |
| Others | All other purchases |

---

## How Rewards Are Calculated

Transactions are **sorted chronologically** before any calculation runs — this is critical for correct cap tracking across days and months. Rewards accrue in whole-point blocks (e.g. 5 RP per ₹150 spent, rounded down), not as a flat percentage.

```
For each transaction (oldest first):
  1. base amount            = Total Charged − Tax/Fees
  2. blocks earned          = floor(base amount / block size)
  3. base reward             = blocks earned × points per block
  4. potential accel reward = base reward × (category multiplier − 1)
  5. actual accel reward    = min(potential accel reward, daily cap remaining, monthly cap remaining)
  6. missed RP               = potential accel reward − actual accel reward
  7. update cap trackers (combined SmartBuy, or per-category for Voucher/Gyftr/Dining/Utility/Insurance)
  8. total reward (shown)   = base reward + potential accel reward
     actual total reward    = base reward + actual accel reward
```

The table shows both the uncapped "potential" total and tracks how much reward was actually earned (and how much was lost to caps) so you can see the impact of caps at a glance.

### Cap Status Indicators

| Status | Meaning |
|---|---|
| ✅ Full | Full accelerated reward earned |
| 🔴 Daily Cap | Daily limit reached |
| 🟠 Monthly Cap | Monthly limit reached |
| 🟡 Partial | Partially capped |
| ✅ No Cap | Unlimited rewards |

### Transaction Data Structure

```javascript
{
    date: 'YYYY-MM-DD',
    merchant: 'string',              // description
    category: 'string',
    amount: number,                  // Total Charged (full statement amount)
    taxAmount: number,                // Tax/Fees, excluded from reward base
    upfrontDiscount: number,
    baseAmount: number,               // amount − taxAmount, used for reward calc
    cardType: 'string',
    included: boolean,                // from the "Use" column on import
    baseReward: number,               // calculated
    acceleratedReward: number,        // potential (uncapped) accelerated reward
    actualAcceleratedReward: number,  // accelerated reward after caps
    missedRP: number,                 // potential − actual, i.e. lost to caps
    totalReward: number,              // baseReward + potential accelerated reward
    actualTotalReward: number,        // baseReward + actual accelerated reward
    capStatus: 'string'
}
```

---

## Files

| File | Description |
|---|---|
| `index.html` | Main calculator app, served live via GitHub Pages |
| `rewards-calculator.html` | Identical copy of the app, for local/offline use |
| `Axis_Magnus_Rewards_Analysis.md` | Reference guide for Axis Magnus Burgundy rewards |

### Dependencies (CDN)

- [Font Awesome 6.4.0](https://fontawesome.com/) — icons
- [SheetJS 0.18.5](https://sheetjs.com/) — Excel import/export

---

## Troubleshooting

**"Please select a card type first"** — Select an HDFC card from the dropdown before adding transactions.

**Excel import shows "X rows skipped"** — Check that skipped rows have valid dates, non-zero amounts, and categories that match the selected card.

**Rewards seem incorrect** — Ensure you understand how daily/monthly caps work; the tool processes transactions in date order, so earlier transactions consume cap headroom first.

**Export doesn't trigger a download** — Ensure at least one transaction exists and that your browser allows file downloads from local pages.

Open the browser console (`F12`) for detailed error messages during import and validation.

---

## Future Enhancement Ideas

- Local storage to persist data across sessions
- Per-category reward breakdown charts
- Cap utilization progress bars
- Side-by-side multi-card comparison
- Milestone alerts when approaching caps
- PDF report export
- "Best card for this transaction" suggestion

---

## Version History

### v1.2 — November 5, 2025
- Complete SmartBuy category overhaul based on actual SmartBuy portal
- Added SmartBuy subcategories (Hotels, Flights, Trains, Buses, Shop Partners, Vouchers)
- Added Weekend Dining category for Diners Black Metal
- Updated multipliers and monthly caps to match current card benefits
- Intuitive category naming (e.g. "Travel - Flights (SmartBuy)")

### v1.1 — November 5, 2025
- Added Gyftr category for all HDFC cards
- Tax handling for Gyftr purchases (GST excluded from reward base)
- Excel import/export updated to support tax fields

### v1.0 — November 5, 2025
- Initial release: multi-card support, cap engine, Excel import/export, validation, premium UI

---

## Share This

Live app: **https://williamscott701.github.io/HDFC-DCBM-Rewards-Calculator/**
Repo: **https://github.com/williamscott701/HDFC-DCBM-Rewards-Calculator**

Ready-to-post blurbs if you want to share it:

**Reddit / forum post**
> Built a free, open-source rewards calculator for HDFC's premium metal cards (Infinia, Diners Club Black, Regalia, Diners Privilege). It tracks daily/monthly SmartBuy caps transaction-by-transaction so you actually know when you've hit your cap instead of guessing. Runs entirely in the browser — no signup, no data sent anywhere. Supports Excel import/export. Feedback welcome: https://williamscott701.github.io/HDFC-DCBM-Rewards-Calculator/

**Twitter / X**
> Track your HDFC Infinia / Diners Club Black Metal rewards down to the daily & monthly SmartBuy cap — free, open-source, runs 100% in your browser. No signup, no tracking. 🔗 https://williamscott701.github.io/HDFC-DCBM-Rewards-Calculator/ #HDFC #CreditCards #PersonalFinance

**LinkedIn**
> Sharing a small open-source tool I built: a rewards calculator for premium HDFC credit cards (Infinia Metal, Diners Club Black Metal, Regalia, Diners Privilege). It replicates HDFC's actual SmartBuy cap logic — daily & monthly caps, processed in chronological order — so you can see exactly how much accelerated reward you've earned and how much cap headroom is left. Fully client-side, Excel import/export, MIT licensed. Try it: https://williamscott701.github.io/HDFC-DCBM-Rewards-Calculator/

## Contributing

Issues and PRs are welcome. If reward structures for a card have changed, please open an issue with a source link (bank T&Cs or official SmartBuy page).

## License

MIT — see [LICENSE](LICENSE)

## Disclaimer

This tool is provided for personal convenience only. Reward rates, caps, and structures change frequently — always verify with official HDFC and Axis Bank terms and conditions before making financial decisions.
