# Paycheck Calculator

Single-file web app for forecasting monthly take-home pay with accurate federal/state tax withholding, 401k, stock vests, and WA state taxes.

## Annual Updates

Each year, the following values in `paycheck-calculator.html` need to be updated from official sources. All are in the `<script>` section near the top of the file.

### 1. Federal Withholding Tables (IRS Pub 15-T)

**Source:** https://www.irs.gov/publications/p15t → "Percentage Method Tables for Automated Payroll Systems" (Section 1, typically page 12 of the PDF)

**Location in code:** The `TABLES` constant (search for `// 2026 IRS Pub 15-T`)

There are 6 tables to update:
- `mfj` — Standard withholding, Married Filing Jointly
- `single` — Standard withholding, Single or Married Filing Separately
- `hoh` — Standard withholding, Head of Household
- `step2_mfj` — Step 2 Checkbox, Married Filing Jointly
- `step2_single` — Step 2 Checkbox, Single or MFS
- `step2_hoh` — Step 2 Checkbox, Head of Household

Each row format: `[bracketFloor, bracketCeiling, baseTax, rate, excessOver]`

The publication has two side-by-side tables on one page:
- Left side = "STANDARD Withholding Rate Schedules" → `mfj`, `single`, `hoh`
- Right side = "Form W-4, Step 2, Checkbox, Withholding Rate Schedules" → `step2_mfj`, `step2_single`, `step2_hoh`

**Important:** These are the *withholding* tables (Pub 15-T), NOT the income tax filing brackets from irs.gov/filing. They are different numbers.

### 2. Social Security Wage Base

**Source:** https://www.ssa.gov/oact/cola/cbb.html

**Location in code:** Default value in `state.ssBase` (also configurable in the UI)

- 2026: $184,500

### 3. 401k Annual Contribution Limit

**Source:** https://www.irs.gov/retirement-plans/plan-participant-employee/retirement-topics-401k-and-profit-sharing-plan-contribution-limits

**Location in code:** Default value in `state.cap401k`

- 2026: $23,500 (under 50), $31,000 (50+, if catch-up applies)

### 4. Additional Medicare Tax Threshold

**Source:** https://www.irs.gov/businesses/small-businesses-self-employed/questions-and-answers-for-the-additional-medicare-tax

**Location in code:** Default value in `state.medicareThresh`

- This has been $200,000 since 2013 and is NOT indexed for inflation. Unlikely to change unless Congress acts.

### 5. WA Paid Family & Medical Leave (PFML) Rate

**Source:** https://paidleave.wa.gov/employers/ (announced each fall for the following year)

**Location in code:** Default value in `state.pfmlRate` (stored as decimal, displayed as %)

- 2026: Total rate 1.13%, employee share ≈ 71.43% → **0.807159%** (0.00807159)
- Wage cap = SS wage base ($184,500 for 2026)

### 6. WA LTCare (WA Cares Fund) Rate

**Source:** https://wacaresfund.wa.gov/

**Location in code:** Default value in `state.ltcareRate`

- 2026: 0.58% (0.0058), no wage cap
- This rate may change annually

### 7. WA PFML Wage Cap

**Location in code:** Default value in `state.pfmlCap`

- Typically equals the SS wage base. Update if they diverge.

## What Does NOT Need Code Changes

These are all configurable in the UI and saved in localStorage / export files:
- Salary steps (base salary, effective dates)
- Stock vests (dates, shares, prices, withholding %)
- 401k percentage per month
- Pre-tax benefits (HSA, medical, dental, vision)
- Post-tax benefits (supp ins)
- Imputed income, employer life insurance
- Filing status, Step 2 checkbox

## Quick Update Procedure

1. Download the new year's Pub 15-T PDF from irs.gov
2. Find the "Percentage Method Tables for Automated Payroll Systems" page
3. Update the 6 bracket tables in the `TABLES` constant
4. Update `ssBase`, `cap401k`, `pfmlRate`, `pfmlCap`, `ltcareRate` defaults if changed
5. Update the year in `state.year`
6. Test against a known pay stub from the new year

## Stock Price Fetch

Uses Finnhub API (free tier, 60 calls/min). Get a key at https://finnhub.io — it's stored in localStorage separately from the export file.

## Import/Export

- **Export** saves all config (salary, vests, benefits, tax settings) as a JSON file
- **Import** restores from a previously exported JSON file
- The Finnhub API key is NOT included in exports (stored separately in localStorage)
