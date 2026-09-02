# Does Discounting Actually Sell More Units?
### A Hypothesis Test on the Superstore Dataset

## Overview

There is a widespread assumption in retail folklore: *"stores use discounts to trick
customers into buying more, and end up making more money as a result."* This project puts
the first half of that claim to a formal statistical test using the Superstore dataset
(Phase 1 – Data Preparation & Warehouse).

**Research question:** Does applying a discount to an order line lead to a statistically
and practically significant change in the **quantity of units sold**, compared to items
sold at full price?

## Repository Contents

| File | Description |
|---|---|
| `Discount_Impact_on_Quantity_Sold.ipynb` | Main analysis notebook (Python) |
| `data/` | Expected location of the input CSV file (not included — see *Data* below) |
| `README.md` | This file |

## Methodology

The notebook follows a standard hypothesis-testing workflow:

1. **Data loading & validation** — load the raw order-level file, coerce `Discount` and
   `Quantity` to numeric, and drop invalid/missing rows.
2. **Group construction** — split all order lines into two groups:
   - **Discounted**: `Discount > 0`
   - **Full Price**: `Discount == 0`
3. **Exploratory analysis** — descriptive statistics (mean, median, standard deviation,
   skewness, kurtosis) and visualizations (histogram/KDE, boxplot, violin plot) of the
   `Quantity` distribution in each group.
4. **Assumption checking** — Q-Q plots and a D'Agostino-Pearson normality test per group,
   plus Levene's test for homogeneity of variance, to justify the choice of statistical
   test.
5. **Hypothesis testing**
   - **Primary test — Mann-Whitney U test**: a non-parametric test appropriate for the
     right-skewed, discrete nature of `Quantity`, used to compare the two distributions
     without assuming normality.
   - **Robustness check — Welch's t-test**: run in parallel as a secondary check, valid on
     large samples via the Central Limit Theorem even though the raw data is non-normal.
6. **Effect size** — rank-biserial correlation (for Mann-Whitney U) and Cohen's *d*
   (for the t-test), because a statistically significant result on a large dataset can
   still be practically meaningless without a meaningful effect size.
7. **Business sanity check (optional)** — a brief look at total `Sales` and `Profit` by
   group, to distinguish "discounts move more units" from "discounts make more money" —
   two separate claims that are often conflated.

## Hypotheses

- **H0 (null):** The distribution of `Quantity` sold is the same for discounted and
  full-price order lines.
- **H1 (alternative):** The distribution of `Quantity` sold differs between the two groups.

Significance level: **α = 0.05**.

## Data

This notebook expects the classic Superstore order-level export (commonly distributed as
`Sample - Superstore.csv` / `Sample-Superstore.csv`) with, at minimum, the following
columns:

| Column | Type | Description |
|---|---|---|
| `Discount` | numeric | Discount rate applied to the order line (`0` = no discount) |
| `Quantity` | numeric | Units sold in that order line |
| `Sales` *(optional)* | numeric | Revenue for the order line — enables the business sanity check |
| `Profit` *(optional)* | numeric | Profit for the order line — enables the business sanity check |

Place your copy of the dataset at `data/Sample-Superstore.csv`, or update the `DATA_PATH`
variable at the top of the *Load Data* section of the notebook to point at your file.

> The dataset itself is not redistributed in this repository. Source it from the Phase 1
> data preparation output of this project (or the original Kaggle "Sample Superstore"
> dataset, which shares the same schema).

## How to Run

```bash
# 1. Create and activate a virtual environment (recommended)
python3 -m venv .venv
source .venv/bin/activate   # on Windows: .venv\Scripts\activate

# 2. Install dependencies
pip install pandas numpy matplotlib seaborn scipy jupyter

# 3. Place your data file at data/Sample-Superstore.csv (see Data section above)

# 4. Launch Jupyter and run all cells
jupyter notebook Discount_Impact_on_Quantity_Sold.ipynb
```

## Interpreting the Results

The notebook produces both a **p-value** and an **effect size** for each test. Read them
together, not the p-value alone:

- A **small p-value** (< 0.05) only tells you a difference between groups is *unlikely to
  be due to chance* — with thousands of rows, even tiny, commercially unimportant
  differences often become "statistically significant."
- The **effect size** (rank-biserial *r* or Cohen's *d*) tells you how *large* that
  difference actually is in practical terms. Use the standard interpretation bands
  documented inline in the notebook (negligible / small / medium / large) to judge
  whether the difference is meaningful for the business.

The notebook's final section provides a template for writing up the statistical and
business conclusions once it has been run against the real dataset.

## Limitations & Notes

- `Quantity` is analyzed at the **order-line level**, not aggregated per customer or per
  transaction; this matches the grain of the raw Superstore export.
- The Mann-Whitney U test evaluates whether one distribution is stochastically greater
  than the other, not strictly a difference in medians — this is stated explicitly because
  it affects how the result should be worded in a report.
- The optional revenue/profit check is descriptive only; it does not include a formal
  hypothesis test, since the notebook's primary scope is the *quantity* question.
