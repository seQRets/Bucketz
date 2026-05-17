# 🪣 Bucketz Portfolio Tracker

A single-file investment portfolio tracker for modeling a multi-bucket retirement allocation, with per-holding tax-aware income calculations and long-horizon growth projections.

> [!WARNING]
> **Not financial advice.** Bucketz is a research and planning tool. It performs the calculations you ask it to using the numbers you enter — nothing more. It does not account for your full financial situation, does not guarantee any outcome, and should not be used as the sole basis for investment, tax, or retirement decisions. Tax brackets, rules, and account regulations change over time; figures shown in the app reflect 2025 assumptions and may be out of date. Consult a licensed financial advisor, CPA, or tax professional before acting on anything you see here.

No build step, no dependencies, no server. Open `index.html` in any modern browser and the app runs locally; all data persists to your browser's `localStorage`.

## Running it

```bash
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

Or drag the file into a browser window. That's it.

## What it models

A **4-bucket portfolio** (with an optional 5th aggressive bucket), where each holding is tagged with the account type that holds it (Taxable, Traditional, Roth, HSA) and the kind of income it generates (Ordinary, Qualified, Tax-free):

| Bucket | Purpose |
| --- | --- |
| **Cash** | Spending reserve (set as a dollar amount, not a percentage) |
| **Dividend** | Yield-focused income generation |
| **Foundation** | Core broad-market exposure |
| **Growth** | Long-term appreciation |
| **Hypergrowth** | Individual stocks / crypto (only shown under the Aggressive preset) |

The Cash bucket is dollar-allocated; the rest are percentage-allocated against the remainder.

## Two views

A toggle below the header switches between two ways of looking at the same data:

### Buckets view
Editable. Each bucket card shows its target allocation, current yield, and rolled-up income, with a holdings table where you set ticker, weight or amount, yield, account, and income type. Weight and amount are bidirectionally bound — the source of truth is amount, weight is derived as `amount / bucket_target × 100`.

### Accounts view
Read-only re-grouping of the same holdings, organized by account type (Taxable, Traditional, Roth, HSA). Each account card shows the target you've set, used / left, weighted yield, and annual income (with after-tax shown for accounts with taxable holdings). Useful for answering "where do I actually need to hold each ticker to achieve my bucket plan?"

## Inputs

- **Total portfolio** — the dollar amount you're allocating.
- **Marginal rate** — your combined federal + state ordinary-income rate. Applies to interest, non-qualified dividends, and all Traditional withdrawals. The `?` badge shows the 2025 federal brackets.
- **Qualified / LTCG rate** — your preferential rate for qualified dividends and long-term gains in taxable accounts. The `?` badge shows the 2025 LTCG brackets.
- **Account targets** — optional dollar allocations per account type, with used/left tracking and a warning if they don't sum to your total portfolio.
- **Monthly spending** — used by presets to auto-fill the cash dollar amount based on the recommended months of expenses.

## Allocation presets

Five strategy chips set bucket percentages and recommended months of cash:

| Preset | Dividend | Foundation | Growth | Hypergrowth | Cash (months) |
| --- | --- | --- | --- | --- | --- |
| Aggressive | 30% | 30% | 30% | 10% | 3 |
| Moderate | 30% | 35% | 35% | — | 6 |
| Conservative | 40% | 30% | 30% | — | 12 |
| Low Risk | 40% | 40% | 20% | — | 24 |
| Retirement | 50% | 40% | 10% | — | 36 |

A **Manual** chip clears the active preset without touching your allocations, so you can fine-tune freely.

## Tax-aware income

For each holding the app computes annual income (`amount × yield`) and an after-tax factor based on the account and income type:

| Account | Tax rate applied |
| --- | --- |
| Roth | 0% |
| HSA | 0% (but see HSA note below) |
| Traditional | Marginal rate |
| Taxable / Tax-free income type | 0% |
| Taxable / Qualified income type | Qualified rate |
| Taxable / Ordinary income type | Marginal rate |

The bottom totals row summarizes monthly pre-tax, tax-free, taxable, and after-tax income, plus portfolio yield.

### HSA handling

HSA holdings are tracked for asset allocation but **excluded** from income/yield totals, since HSA funds aren't generally available for daily spending until age 65. The HSA card in account view explains availability rules in its tooltip; per-row HSA income is displayed in muted italics so you can see what the holding generates without it inflating your spendable totals.

## Growth projections

A line chart at the bottom plots portfolio value (or projected monthly income) over time at 3%, 5%, 7%, and 10% annual return rates. Assumes income reinvests at the current portfolio yield; no withdrawals, no inflation adjustment.

Use the **Years** slider to set the horizon (5–100). Hover the chart for a per-year tooltip across all four rate scenarios. Toggle **Portfolio Value** / **Monthly Income** to switch what's plotted.

## Persistence and data portability

- **localStorage** — everything you enter persists automatically under key `retirement-tracker-v1`.
- **Export** — downloads the current state as a timestamped JSON file.
- **Import** — restores state from a previously exported JSON.
- **Erase All** — wipes everything (with confirmation).

## Tech notes

Pure HTML/CSS/JS in a single ~1700-line file. No external libraries. Mobile-responsive via media queries placed at the end of the `<style>` block. The render layer splits into `buildDom()` (full rebuild on add/remove/preset/import) and `refresh()` (per-keystroke calculated-text + non-focused input updates), so typing into fields never gets clobbered by re-renders.

## License

See [LICENSE](LICENSE).
