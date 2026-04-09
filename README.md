[README.md](https://github.com/user-attachments/files/26600046/README.md)
# Top-up Loan Net Disbursal Calculator

An internal calculator for sales and business teams to quickly compute the **net disbursal amount** and **monthly EMI** of a top-up loan during live customer calls. Built to eliminate manual subtraction work and speed up the on-call pitch.

**Live URL:** `https://<your-username>.github.io/<repo-name>/`

---

## Why this exists

Sales reps were spending 2–3 minutes per call manually calculating the net disbursal amount on a calculator app — sanctioned amount minus outstanding loan, processing fee with GST, doc charges, insurance premium, VAS healthcare fee. This is error-prone, slows down the pitch, and erodes customer trust when the rep fumbles the math.

This calculator does the entire computation in real time as the rep types, gives them a clean breakdown to walk the customer through, and provides one-tap copy/screenshot to share the summary on WhatsApp, email, or Slack.

---

## Features

- **Zero backend, zero build step.** Single `index.html` file. Hand-written CSS, vanilla JavaScript. No frameworks, no `npm install`, no toolchain.
- **Real-time calculation** as the user types — no "Calculate" button to click.
- **Indian number formatting** (₹50,00,000 not ₹5,000,000) on all amount inputs.
- **Two key outputs** in the summary card:
  - **Net Disbursal Amount** (the super metric)
  - **Monthly EMI** computed via the standard reducing-balance formula
- **Full breakdown** showing every deduction line item and a **Total Deductions** subtotal.
- **Negative disbursal warning** when inputs result in a net amount below zero.
- **Share actions:**
  - **Copy** — copies a clean plain-text summary that pastes well into WhatsApp, email, Slack, or Notes
  - **Image** — exports a PNG screenshot of the summary card (saved to Downloads folder)
- **Reset / New Calculation** button to clear all fields in one click.
- **Helper tooltips** on fields where sales reps need pointers (Insurance Premium → Jarvis, Outstanding Loan → Jarvis, Monthly EMI → BPI exclusion note).
- **Fully responsive.** Works end-to-end on desktop, tablet, and mobile. iOS Safari auto-zoom prevented. No horizontal scroll on any viewport down to 320px.
- **Brand-aligned** with company colors (`#003381`, `#9187E7`, `#4EA893`, `#E87659`).

---

## Calculation Logic

### Net Disbursal Amount

```
PF (incl GST) = Sanctioned Amount × PF% × 1.18

Total Deductions = Outstanding Loan
                 + PF (incl GST)
                 + ₹589 (Documentation Charges)
                 + Insurance Premium
                 + VAS Healthcare

Net Disbursal = Sanctioned Amount − Total Deductions
```

### Monthly EMI (Reducing Balance)

```
EMI = P × r × (1+r)^n / ((1+r)^n − 1)

where:
P = Sanctioned Loan Amount
r = IRR ÷ 12 ÷ 100   (monthly interest rate)
n = Tenure in months
```

If IRR is 0, EMI falls back to simple division `P / n`.

> **Note:** EMI shown excludes Broken Period Interest (BPI), which may be added at the time of disbursal depending on the disbursal date. This is called out in a tooltip next to the Monthly EMI label.

### Constants

| Item | Value |
|---|---|
| Documentation Charges | ₹589 |
| GST on Processing Fee | 18% |
| VAS Bronze Plan | ₹4,000 |
| VAS Silver Plan | ₹7,000 |
| VAS Gold Plan | ₹10,000 |
| VAS Platinum Plan | ₹12,500 |
| PF slabs | 1% to 5% in 0.25% increments |

### Inputs that don't affect Net Disbursal

**Tenure** and **IRR** are captured as inputs but are only used to compute the EMI. They don't reduce the net disbursal amount — that's by design.

---

## Field Reference

| Field | Type | Notes |
|---|---|---|
| Sanctioned Loan Amount | ₹ amount | Provided by credit/risk team |
| Total Outstanding Loan Amount | ₹ amount | Pulled from Jarvis (changes per day) |
| Tenure | months | Used only for EMI calculation |
| IRR / RACC ROI | % | Used only for EMI calculation |
| Processing Fee (excl. GST) | dropdown 1%–5% | 0.25% increments |
| Processing Fee (incl. GST) | auto-calculated | Read-only |
| Documentation Charges | fixed ₹589 | Read-only |
| Insurance Provider | dropdown | Acko / ICICI Lombard |
| Insurance Premium | ₹ amount | Pulled from Jarvis |
| VAS Provider | dropdown | DocOnline / Medibuddy |
| VAS Plan | dropdown | Bronze / Silver / Gold / Platinum (hardcoded amounts) |

---

## Local Usage

Just open `index.html` in any modern browser — Chrome, Edge, Safari, or Firefox. No server needed. No installation needed.

**One caveat:** when running locally via `file://`, the Copy button uses a `document.execCommand` fallback because the modern Clipboard API requires HTTPS. Once deployed to GitHub Pages (HTTPS), it uses the modern API. Both paths work — you'll never see "Copy failed".

---

## Deploy to GitHub Pages

1. Create a new **public** repo on GitHub (any name).
2. Upload `index.html` and `README.md` via the GitHub web UI (Add file → Upload files → drag → Commit changes).
3. Go to **Settings → Pages**.
4. Under **Source**, select branch `main` and folder `/ (root)`. Click **Save**.
5. Wait 30–60 seconds. Your live URL will be `https://<your-username>.github.io/<repo-name>/`.
6. Share the URL with the sales team. Bookmark it.

For future updates, edit `index.html` directly in the GitHub web UI (pencil icon → make changes → Commit changes). Changes go live within a minute.

---

## Tech Stack

| Layer | What |
|---|---|
| Markup | Single HTML file |
| Styling | Hand-written CSS (no Tailwind, no preprocessor) |
| Logic | Vanilla JavaScript |
| Fonts | Inter (Google Fonts CDN) |
| Screenshot | html2canvas (cdnjs CDN) |
| Hosting | GitHub Pages |

Total external dependencies: **2 CDN imports** (Inter font + html2canvas). No build step. No package manager.

---

## File Structure

```
topup-loan-calculator/
├── index.html       # The entire app — markup, styles, and logic
└── README.md        # This file
```

---

## Browser Support

Tested on:
- Chrome / Edge (latest)
- Safari (desktop + iOS)
- Firefox (latest)
- Samsung Internet

Responsive breakpoints:
- **Desktop (>960px):** 2-column layout, sticky summary card
- **Tablet (768–960px):** 1-column stacked layout
- **Mobile (<768px):** 1-column with tightened spacing, 16px input font to prevent iOS auto-zoom
- **Small phones (<420px):** Further-tightened spacing, smaller hero font

---

## Known Limitations / v1 Scope

These are deliberately out of scope for v1 and can be added later:

- **No app form ID input.** Sales reps don't tag the calc to a specific case.
- **No save/history.** Each session is fresh; closing the tab loses inputs.
- **No multi-currency.** INR only.
- **No authentication.** Anyone with the URL can use it. Suitable for an internal-only URL; not for public-facing customer use.
- **VAS plan amounts and Doc charges are hardcoded.** If pricing changes, edit the constants at the top of the `<script>` block in `index.html`.
- **EMI excludes Broken Period Interest.** Customers may see a slightly different first EMI on the loan agreement.

---

## Roadmap (potential v2 ideas)

- App form ID field for case-tagging
- Per-user history of recent calculations (localStorage)
- PDF export of the summary instead of PNG
- Support for additional insurance and VAS providers
- BPI calculation if disbursal date is provided
- Sales team usage analytics (which slabs are most common, etc.)
- Authentication / SSO if hosted on internal infra

---

## Contributing

This is an internal tool. To suggest changes, open an issue on the repo or ping the product owner directly.

---

## Changelog

**v1.0** — Initial release
- Net disbursal calculation with all standard deductions
- Monthly EMI calculation
- Total Deductions subtotal
- Copy and PNG export
- Indian number formatting
- Brand colors applied
- Full responsive layout

---

**Internal use only.**
