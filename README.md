# HDFC Credit Card Rewards Calculator

A browser-based rewards calculator for premium HDFC credit cards. No installs, no backend — just open the HTML file and start tracking.

Also includes a detailed reference analysis for the **Axis Magnus Burgundy** card.

---

## Features

- **Multi-card support** — Infinia Metal, Diners Club Black Metal, Regalia / Regalia Gold, Diners Privilege
- **Accurate cap tracking** — per-category daily and monthly caps, processed in chronological order
- **Accelerated rewards** — SmartBuy portal categories (Hotels, Flights, Gyftr, Amazon, Flipkart, Dining, and more)
- **Excel import / export** — load transactions from `.xlsx` and export results via [SheetJS](https://sheetjs.com/)
- **Zero data leaves your device** — everything runs locally in the browser; no server, no tracking

---

## Usage

### Getting Started

1. Download or clone this repo
2. Open `rewards-calculator.html` in any modern browser
3. Select your HDFC card from the dropdown
4. Add transactions and view rewards in real-time

No build step or internet connection required (CDN assets load on first open and may be cached).

### Adding Transactions Manually

1. Select the transaction date
2. Enter the merchant name
3. Choose a category
4. Enter the amount
5. Click **Add Transaction**

### Importing from Excel

1. Click **Load Excel File** and select your `.xlsx` file
2. Required columns: `Date`, `Merchant`, `Category`, `Amount`
3. Optional columns: `Base Amount`, `Tax Amount`, `Includes Tax`, `Tax Percent`
4. Invalid rows are skipped with a warning report

### Exporting Data

Click **Export to Excel** — all transactions with calculated rewards download automatically. The filename includes the card name and current date.

### Downloading a Template

Click **Download Template** to get a pre-formatted Excel file with the correct columns and categories for the selected card.

---

## Supported Cards

| Card | Base Rate | Notable Accelerators |
|---|---|---|
| HDFC Infinia Metal | 3.33% | 5x SmartBuy (Hotels, Flights, Vouchers, Gyftr) — ₹15K/month cap per category |
| HDFC Diners Club Black Metal | 3.33% | 10x Hotels, 5x Flights/Bus, 3x Train — ₹50K/month cap; unlimited Weekend Dining |
| HDFC Regalia / Regalia Gold | 2.5% | 5x SmartBuy (Flights, Hotels, Vouchers, Gyftr) — ₹12.5–15K/month cap per category |
| HDFC Diners Privilege | 1.33% | SmartBuy categories |

### Category Groups

| Group | Categories |
|---|---|
| Travel | Flights, Hotels, Bus, Train (all via SmartBuy) |
| Shopping | Amazon, Flipkart, Partner Stores, Online |
| Food | Swiggy, Food Delivery, Dining (incl. Weekend Dining) |
| Gift Vouchers | SmartBuy Vouchers, Gyftr |
| Wallets | PayZapp |
| Others | All other purchases |

---

## How Rewards Are Calculated

Transactions are **sorted chronologically** before any calculation runs — this is critical for correct cap tracking across days and months.

```
For each transaction (oldest first):
  1. base reward        = amount × base rate
  2. potential accel    = amount × (accelerated rate − base rate)
  3. actual accel       = min(potential, daily cap remaining, monthly cap remaining)
  4. update cap trackers
  5. total reward       = base reward + actual accel
```

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
    merchant: 'string',
    category: 'string',
    amount: number,           // total amount paid
    baseAmount: number,       // amount used for reward calc (excl. tax)
    taxAmount: number,
    includesTax: boolean,
    taxPercent: number,
    cardType: 'string',
    baseReward: number,       // calculated
    acceleratedReward: number,
    totalReward: number,
    capStatus: 'string'
}
```

---

## Files

| File | Description |
|---|---|
| `rewards-calculator.html` | Main calculator app (single self-contained file) |
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

## Contributing

Issues and PRs are welcome. If reward structures for a card have changed, please open an issue with a source link (bank T&Cs or official SmartBuy page).

## License

MIT — see [LICENSE](LICENSE)

## Disclaimer

This tool is provided for personal convenience only. Reward rates, caps, and structures change frequently — always verify with official HDFC and Axis Bank terms and conditions before making financial decisions.
