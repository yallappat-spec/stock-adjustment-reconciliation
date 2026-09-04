# Stock Adjustment Reconciliation

A single-page browser tool that matches the **audit variance declaration** (what a
physical count says is short or in excess) against the **stock adjustment report**
(what was actually posted in the system), on barcode and store code, and surfaces
only what doesn't agree.

Everything runs in the browser. No server, no upload, no build step — the two
reports never leave the machine they are opened on.

## Using it

1. Load the two reports — `.xlsx`, `.xls`, `.xlsm` or `.csv`, drag-drop or click.
2. Confirm the columns. The tool guesses the header row and the barcode, store,
   quantity, value, date and item-name columns from the headers; correct anything
   it got wrong.
3. Reconcile. Filter by finding, search a barcode or store, sort any column, and
   download what's on screen as CSV. If MRP is mapped on both reports, a second tab
   compares MRP as well.

### Findings

| Finding | Meaning |
| --- | --- |
| **Not adjusted** | Declared in the audit variance, no adjustment passed |
| **No audit backing** | Adjustment passed with no matching variance declaration |
| **Direction mismatch** | Excess declared but shortage adjusted, or the reverse |
| **Quantity mismatch** | Both sides present, quantities differ |
| **Matched** | Variance and adjustment agree |

### MRP comparison

Map the **MRP** column on both reports and a second tab appears, comparing the MRP
each report carries for the same barcode and store. It only covers pairs present in
both reports — a barcode with no adjustment, or no declaration, has nothing to
compare against and is left to the reconciliation tab.

| Finding | Meaning |
| --- | --- |
| **MRP mismatch** | In both reports, MRP differs |
| **Multiple MRPs** | One barcode carries more than one MRP within a single report |
| **MRP missing** | MRP present on one side only |
| **MRP agrees** | MRP matches on both sides |

The tab lists the exceptions and has its own search and CSV download. MRP is a unit
rate, so it is never summed the way quantities are — where a barcode carries several
MRPs inside one file, every distinct value is listed and no gap is reported, since a
gap against one of several prices would be arbitrary.

Leave the MRP column unmapped and the tabs stay hidden; the tool behaves exactly as
it did before.

### Notes on matching

- Rows are grouped by `barcode + store code` and quantities are summed, so an item
  spread over several lines is compared as one figure. The **Lines** column shows
  the variance/adjustment line counts behind each row.
- Direction is detected from the data, not the header names. A quantity column that
  already contains negatives is treated as signed; otherwise the tool looks for a
  type/reason column whose values actually read as short/excess. Either file can be
  sign-reversed with a checkbox if the two systems use opposite conventions.
- Tick **Match on barcode only** when the two files write store codes differently.
- Rows with a blank barcode are skipped — export total rows land there — and the
  count is reported above the results.
- The table renders the first 4,000 rows; the CSV download has everything.

## Running it locally

Open `index.html` in a browser. That's all — there is nothing to install.

## Layout

```
index.html                  the entire application
vendor/xlsx.full.min.js     SheetJS, used to parse the workbooks
```

## Privacy

Reports are read with the browser's `FileReader` and parsed in memory. Nothing is
sent anywhere, and no report data is committed to this repository.
