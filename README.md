# TallyBridge

**Turn bank statements into Tally vouchers - right in your browser.**

TallyBridge is a single-page web app that clears bank statement transactions against your Tally ledgers and generates ready-to-import `Masters.xml` and `Vouchers.xml` files for TallyPrime. No install, no server, no accounts: everything runs locally in your browser and your financial data never leaves your machine.

---

## Why TallyBridge?

Manually entering a bank statement into Tally means typing hundreds of vouchers one by one. TallyBridge flips that around:

1. Export your ledgers from Tally as a `.txt` file.
2. Drop your bank statement Excel file into TallyBridge.
3. Map each transaction to the right ledger (one at a time, or in bulk).
4. Download two XML files and import them straight into Tally.

A whole bank statement becomes a handful of clicks.

## Features

- **Batch-based workflow** - every statement is a batch tied to a Tally company and bank account. Resume any batch later, track cleared vs. pending progress on the dashboard, and search batches by company or bank name.
- **Smart clearing table** - filter by date range, particulars, debit/credit amount range, voucher type, or mapped ledger; sort by date or amount; paginate through large statements 100 rows at a time.
- **Bulk ledger mapping** - select any set of rows (including "all filtered") and assign one ledger to all of them in a single action.
- **Create ledgers on the fly** - missing a ledger? Create it mid-flow with its Tally group (all 28 standard Tally groups), opening balance, and Dr/Cr - it gets exported to `Masters.xml` later.
- **Automatic voucher classification** - a debit on the statement becomes a **Payment** voucher, a credit becomes a **Receipt** voucher, and the bank ledger is placed on the correct side of the entry automatically.
- **Export uncleared entries** - download the transactions you couldn't map yet as a formatted Excel file for follow-up.
- **Backup & portability** - export a whole batch (ledgers + pending + finalised transactions) as a JSON file and re-import it on any machine or browser. ID conflicts are resolved automatically.
- **Fully offline-capable data** - all work is stored in your browser's IndexedDB; nothing is uploaded anywhere.
- **Built-in help** - a full in-app Documentation page and a downloadable statement template are one click away.

## Quick Start

> No build step. Open `TallyBridge.html` in any modern browser (an internet connection is needed once, to load React and SheetJS from their CDNs).

### 1. Start a new batch
Open `TallyBridge.html` → **Start with New Bank Statement** → enter your Tally **company name**.

### 2. Import your Tally masters
In Tally: **Gateway of Tally → Alt+E (Export) → Masters → C (Configure)** → set *File Format* to **ASCII (Comma delimited)** → save with **Ctrl+A** → **E (Send)** to get the `.txt` file. Upload it in Step 1 of TallyBridge.

### 3. Import your bank statement
Click **Download Template** to get `bank_statement.xlsx`, fill it with your statement, and upload it. Then pick the bank account ledger this statement belongs to (or create it right there).

**Template columns:**

| Column | Meaning |
|---|---|
| `Date` | Display date |
| `Particulars` | Transaction narration - becomes the voucher narration |
| `Debit(Rs.)` | Withdrawal / payment amount |
| `Credit(Rs.)` | Deposit / receipt amount |
| `Day` / `Month` / `Year` | Numeric date parts used to build the voucher date |

> Debit/Credit follow standard bank statement conventions for all account types (Savings, Current, CC, OD).

### 4. Clear the transactions
On the **Clearing** page, map each row to a Tally ledger using the dropdown, filters, and **Bulk Map**. Click **Push Mapped to Finalise** when you're done.

### 5. Review and export
On the **Finalise** page, review the exact vouchers that will be created. Push mistakes back to Clearing, review or edit the custom ledgers you created, then generate:

1. **`Masters.xml`** - every new ledger you created, with parent group and opening balance.
2. **`Vouchers.xml`** - every cleared transaction as a Payment or Receipt voucher, dated `YYYYMMDD` with a stable GUID.

### 6. Import into Tally

> ⚠️ **Always take a backup of your Tally company data before importing.**

- **Masters first:** Gateway of Tally → **Import Data → Masters** → point to `Masters.xml` → set Behaviour to **Modify with new data**.
- **Then vouchers:** **Import Data → Vouchers** → point to `Vouchers.xml`.
- Verify in the Day Book or ledger reports.

## How the XML works

- **Masters.xml** wraps each new ledger in a Tally `Import Data → All Masters` request: ledger name, parent group, opening balance with Dr/Cr sign (Dr balances negative, Cr positive, per Tally's convention), cost centres and stock off.
- **Vouchers.xml** builds one `Payment` or `Receipt` voucher per transaction: the bank ledger is debited for receipts and credited for payments, the mapped ledger takes the opposite side, and particulars become the narration.

## Tech Stack

- **Single-file React 18 app** - `TallyBridge.html` contains the entire application (markup, styles, and logic), loaded via CDN with Babel standalone.
- **SheetJS (xlsx 0.18.5)** - parses and writes Excel files in the browser.
- **IndexedDB** - persistent local storage for batches, ledgers, and transactions; session state in `sessionStorage`.
- **Zero backend** - no build tools, no server, no database to run.

## Project Structure

```
TallyBridge/
├── TallyBridge.html      # The entire application (open this file)
├── Documentation.html    # In-app user guide
├── Creator.html          # About-the-creator page
└── bank_statement.xlsx   # Bank statement import template
```

## Good to know

- **Your data stays local.** Clearing data lives in your browser's IndexedDB. Clearing your browser storage deletes your batches - export important batches as JSON backups.
- **Per-browser storage.** Batches don't sync between browsers or machines unless you export and import them.
- **Voucher types.** The tool generates Payment and Receipt vouchers only; other voucher types should be entered in Tally directly.
- **Date safety.** Rows whose dates can't be parsed are skipped during import, so check your template's Day/Month/Year columns.

## Author

**Nandeesh B** - built to take the tedium out of Tally data entry.
