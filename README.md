# UK Gilt & Fixed Notice Account Cash Flow Calculator

A desktop app (Python + tkinter) that projects annual cash flow, income tax, fees and total returns for a portfolio of UK gilts and up to two fixed notice savings accounts, held jointly by two UK taxpayers.

> **Disclaimer:** This is a planning tool, not financial or tax advice. Tax rules, allowances and fee schedules change, and the assumptions below simplify real-world behaviour. Check the figures against your own circumstances and a qualified adviser before acting on them.

## Features

- **Gilts:** enter ask (clean) price, coupon %, maturity date and total amount purchased. Nominal, annual coupon and redemption are inferred automatically.
- **Fixed notice accounts (up to two):** amount, fixed rate, notice length (days or months) and start date. Capital and interest are paid as a single payment on the last day of the notice period. Optional roll-over into a new term at the same rate.
- **UK income tax:** estimated per person for a joint holding (default 50/50), assuming the investments are each person's only income. Calculated by UK tax year.
- **Fees:** bond purchase fees and quarterly platform fees, shown as separate outgoings and subtracted from total returns.
- **Total returns summary:** initial capital, interest, coupons, capital gain/(loss) on redemptions, less tax and fees.
- **Export:** CSV, or Excel (with bold headings) for the full set of results.

## Requirements

- Python 3.8+
- tkinter (included with most Python installers)
  - Debian/Ubuntu: `sudo apt install python3-tk`
- Optional, for Excel export only: `openpyxl`

```bash
pip install openpyxl   # optional
```

## Usage

```bash
python gilt_cashflow.py
```

1. Add each gilt: name, ask price (per £100), coupon %, maturity date (`DD/MM/YYYY`) and the total £ amount purchased.
2. Tick and complete one or two fixed notice accounts, if required.
3. Set the "cash flows from" date, the number of years to project, whether to deduct tax, and each person's ownership share.
4. Press **Calculate cash flow**.
5. Review the tabs, then use **Export CSV** or **Export Excel**.

### Output tabs

| Tab | Contents |
|---|---|
| Total returns | Capital invested, interest, coupons, redemption gains, tax, fees, net return |
| Annual summary | Cash flow per UK tax year including tax and fees |
| Tax breakdown | Each person's allowances used and tax due per tax year |
| Payment schedule | Every individual payment and fee, in date order |

The CSV starts with the total returns block, followed by the annual summary, a fees section, the tax breakdown and the payment schedule. CSV cannot store formatting, so key rows are in capitals; the Excel export applies real bold formatting.

## How it calculates

### Gilts
- Nominal = amount purchased ÷ (ask price ÷ 100)
- Redemption = nominal (paid at par on the maturity date)
- Annual coupon = nominal × coupon %, paid half-yearly. Coupon dates are worked back from maturity in 6-month steps, and the final coupon is paid with the redemption.
- Accrued interest at purchase is ignored; only cash flows after the "from" date are counted.

### Fixed notice accounts
- Simple gross interest: balance × rate × days ÷ 365, paid with the capital on the final day of the notice period.
- With roll-over enabled, interest is paid out each term and the capital is re-deposited at the same rate.

### Income tax
- Gilt coupons and account interest are treated as savings income, taxed in the UK tax year (6 April to 5 April) in which they are paid.
- Each person receives the Personal Allowance (£12,570), the £5,000 starting rate for savings and the Personal Savings Allowance (£1,000 basic rate, £500 higher rate, £0 additional rate). The Personal Allowance tapers above £100,000 of income.
- Savings rates: 20/40/45% up to 5 April 2027, and 22/42/47% from 6 April 2027. These apply across the whole UK, including Scotland.
- Allowances and thresholds are assumed frozen. They live in the `TAX_RULES` dictionary and `savings_rates()` in the source and can be edited.
- Gilt redemption proceeds and account capital are not taxed. Gilts are exempt from Capital Gains Tax.

### Fees
- **Purchase fee**, once per bond on the amount purchased: 0.10% if less than one year to maturity, otherwise 0.25%. The bonds are assumed bought on the "cash flows from" date.
- **Platform fee:** 0.15% a year of total bond value, charged in arrears at each calendar quarter end (31 Mar, 30 Jun, 30 Sep, 31 Dec), pro-rated by days held. Bond value is taken as the amount purchased. Gilts only.
- Rates are in the `FEES` dictionary in the source.
- Fees are not treated as reducing taxable income.

## Limitations

- No live gilt prices; bond value for fees and unredeemed holdings is the amount paid.
- Gilts maturing after the projection window are held at cost, so their capital gain or loss is not counted until they redeem.
- The first and last tax years may be partial, depending on the projection window.
- Tax is modelled for savings income only, with no other income, and no pensions, ISAs, Marriage Allowance or other reliefs.
- Interest and coupons are taxed when paid rather than on an accruals basis.
- Only UK gilts are modelled; index-linked gilts are not supported.

## Project structure

```
.
├── gilt_cashflow.py   # the whole application
└── README.md
```

## Contributing

Issues and pull requests are welcome. If you change tax rules or fee rates, please cite the source (for example, GOV.UK or your platform's fee schedule) in the pull request.

## Licence

Add a licence of your choice before publishing (for example MIT) and place it in a `LICENSE` file.
