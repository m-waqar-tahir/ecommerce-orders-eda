# E-commerce Orders: Exploratory Data Analysis

**In one sentence:** an analysis of 1,200 online orders that found the business's reported revenue is far higher than what it has actually delivered, and that its order-status records cannot be trusted.

Built for **Project 2: Exploratory Data Analysis** of the DecodeLabs Data Analytics Internship (Batch 2026). It uses the cleaned dataset produced in Project 1 (data cleaning).

## What I found

| # | Finding | Evidence |
|---|---|---|
| 1 | **Reported revenue overstates real revenue.** Only 38.6% of order value has been delivered or shipped; 41.1% is cancelled or returned. | 497 of 1,200 orders fail (41.4%) |
| 2 | **The order-status data is unreliable.** 92% of "Pending" orders are over 90 days old. | 218 of 237 orders, about 235,000 in value |
| 3 | **A typical order is worth 824, not 1,054.** A tail of large orders pulls the average up. | Top 20% of orders carry 45.5% of value |
| 4 | **8 unusually large orders** were found by the IQR method and missed by the Z-score method. They are legitimate, so they were kept. | All 8 are maximum-quantity orders at the highest prices |
| 5 | **Nothing explains why orders fail.** No product, payment method, channel or coupon differs meaningfully. | 4 comparisons, all non-significant (p = 0.22 to 0.98) |
| 6 | **First-half revenue is down 19% since 2023**, but the trend is within normal monthly swings. A watch item, not an alarm. | 286,502 to 231,883; trend p = 0.11 |

![Where the order value sits](figures/03_value_by_status.png)

![Pending order age](figures/04_pending_order_age.png)

### What I deliberately did *not* claim

Most EDA projects find a "pattern" in every chart. This one tests every comparison, lists all 14 tests together, and corrects for multiple testing. **One test looked significant on its own (p = 0.005) but did not survive the correction (adjusted p = 0.073)**, which is exactly what one lucky result among 14 tests looks like. It is reported as a hypothesis to re-test, not as a finding.

Also flagged rather than reported as insights:

- `TotalPrice` correlates strongly with quantity and unit price only because it is *defined* as Quantity x UnitPrice.
- `ItemsInCart` correlates with `Quantity` because quantity can never exceed the cart size.

## Recommendations

1. **Audit the order-status process** and close or cancel the stale pending orders.
2. **Record why orders fail** (cancellation and return reason codes plus status timestamps).
3. **Report the median beside the mean**, and use IQR rather than Z-score to flag unusual orders.
4. **Track monthly revenue on a control chart** and revisit after the second half of 2025.
5. **Test coupons in a controlled way** before further discounting.
6. **Follow up the 8 largest orders** as a group of high-value accounts.

## Where to start

| If you have... | Open |
|---|---|
| 5 minutes | [`reports/EDA_Executive_Summary.pdf`](reports/EDA_Executive_Summary.pdf), 4 pages, headlines first |
| 15 minutes | [`notebooks/eda_project2.ipynb`](notebooks/eda_project2.ipynb), the full walkthrough with charts and reasoning |
| Interest in the code | [`src/`](src), modular and tested |

## Method (plain language)

1. **Input:** load the cleaned orders and confirm no missing values or duplicate IDs.
2. **Process:** basic statistics (count, mean, median, five-number summary); outliers by two methods (IQR and Z-score); trends over time; comparisons across segments; correlations; formal tests with correction for running many at once.
3. **Output:** findings written as headlines, translated into business impact, with recommendations and limitations.

Order age is measured against the latest order date in the data (2025-06-30), not today's date, so results are reproducible whenever the code is run.

## Limitations

- The data looks synthetic or simplified: categories are almost perfectly evenly spread and every shipping address is a house number on "Main St", so location cannot be analysed.
- `TotalPrice` ignores coupons and shipping, so discounts and margins cannot be measured.
- `UnitPrice` varies widely within one product (a laptop ranges from about 18 to 700).
- No currency or cost data, so amounts are shown as plain numbers and profit cannot be assessed.
- 2025 covers only January to June, so year-on-year comparisons use January to June for every year.
- The data is observational: nothing here proves cause and effect.

## Repository structure

```
.
├── data/orders_cleaned.csv               # output of Project 1
├── notebooks/eda_project2.ipynb          # full analysis with outputs
├── src/
│   ├── eda_utils.py                      # statistics (outliers, tests, corrections)
│   ├── charts.py                         # stakeholder-style charts
│   ├── run_eda.py                        # writes figures and results JSON
│   └── generate_report.py                # builds the PDF from the results JSON
├── figures/                              # 8 charts (PNG)
├── reports/
│   ├── EDA_Executive_Summary.pdf
│   └── eda_results.json                  # every number quoted in the report
├── tests/test_eda_utils.py               # 11 tests
├── requirements.txt
└── README.md
```

## How to run

```bash
git clone <your-repo-url>
cd <repo-folder>
pip install -r requirements.txt

jupyter notebook notebooks/eda_project2.ipynb   # run all cells

# or, without Jupyter:
python src/run_eda.py            # figures + results JSON
python src/generate_report.py    # executive summary PDF
python -m pytest tests           # 11 tests
```

Every number in the PDF is read from `reports/eda_results.json`, and the last cell of the notebook asserts that the figures quoted in the text match the calculations, so the write-up cannot drift from the data.

## Tech stack

Python 3.10+, pandas, SciPy, Matplotlib, ReportLab, Jupyter, pytest.

## Author

Muhammad Waqar Tahir
