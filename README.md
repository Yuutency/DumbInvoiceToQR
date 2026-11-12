# DumbInvoiceToQR

**Drop in a PDF invoice. Scan a QR code. Paid.**

You get a Belgian invoice as a PDF. To pay it, you type a 16-character IBAN, an amount and a `+++123/4567/89012+++` reference into your banking app, one character at a time, and hope you didn't swap two digits.

DumbInvoiceToQR does that typing for you. It reads the invoice, pulls out the payment details and turns them into an EPC QR code (the "scan to pay" standard used across the SEPA area). Open your banking app, scan, check, confirm.

It's one HTML file and runs offline. Your invoice never leaves your computer.

---

## What it does

- **Reads the PDF for you.** Drag and drop the invoice. The tool finds the payee name, IBAN, BIC, amount, structured reference and due date.
- **Checks before you pay.** The IBAN checksum and the Belgian structured reference (OGM/VCS mod-97 check) are validated live, so a misread digit shows up as a red ✗ instead of a failed transfer.
- **Builds a standard EPC QR code.** Version 002 SEPA Credit Transfer payload, readable by most European banking apps.
- **Tells you when to pay.** Enter or detect the due date and you get one of three answers: *Pay now*, *Overdue*, or *Pay on [date]*, with a 5-day buffer so the money arrives in time.
- **Works around picky banking apps.** Some apps ignore the reference after a scan. Switch between three encodings (digits only, `+++` formatted, or free-text message) until yours picks it up.
- **Lets you correct anything.** Every detected field is editable, and the QR updates as you type.

## Private by design

- No server, no upload, no account, no tracking.
- PDF parsing happens in your browser with a bundled copy of [PDF.js](https://mozilla.github.io/pdf.js/).
- All libraries are in `lib/`. Nothing is loaded from the internet.

You can pull the network cable and it still works.

## Get started

1. Download or clone this repo:
   ```bash
   git clone https://github.com/Yuutency/DumbInvoiceToQR.git
   ```
2. Open `index.html` in your browser.
3. Drop your invoice PDF on the page.
4. Check the fields, then scan the QR with your banking app.
5. **Verify the details in your app before you confirm.** The tool helps; you're still the one signing off.

No install, no build step, no dependencies to fetch.

## Built for Belgian invoices

The parser looks for the Dutch labels found on Belgian invoices (school bills in particular):

| Field | Looks for |
| --- | --- |
| IBAN / BIC | `Rekeningnummer` |
| Amount | `Bedrag` or `Totaal te betalen in EUR` |
| Reference | `+++xxx/xxxx/xxxxx+++` |
| Due date | `Vervaldatum` |
| Payee | `Op naam van` |

Invoice uses a different layout? Fill in or fix the fields by hand and the QR works the same way. Pull requests with patterns for other invoice formats are welcome.

## How it works

```
PDF ──► PDF.js text extraction ──► regex parsing ──► IBAN + OGM validation ──► EPC payload ──► QR code
```

The QR contains a plain EPC payload, viewable under **"QR contents (for debugging)"**:

```
BCD
002
1
SCT
<BIC>
<Payee name>
<IBAN>
EUR<amount>

<structured reference>
<free text>
```

## Tech

- Vanilla HTML, CSS and JavaScript in one file
- [PDF.js](https://mozilla.github.io/pdf.js/) for reading PDFs
- [qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator) for the QR code

## Disclaimer

This tool fills in a payment form for you. It does not make payments. Always compare what your banking app shows with the invoice before you confirm.
