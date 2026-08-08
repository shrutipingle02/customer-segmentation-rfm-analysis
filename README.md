# Customer Segmentation — RFM Analysis

**3,490 customers of an Australian bike retailer, sorted into 11 segments by how
recently they bought, how often, and how much margin they left behind.**

![RFM Analysis dashboard](images/dashboard-1-rfm.png)

## What it found

- **Half the customer base is already leaving.** The five weakest segments hold
  1,726 customers: 49% of the base and 44% of sales ($9.6M). Not a small tail to
  write off.
- **The best customers are a thin slice.** Platinum and Very Loyal together are
  351 people, 10% of the base, 16% of sales. Losing a handful of them costs more
  than losing a hundred at the bottom.
- **Demographics don't separate anybody.** Gender, car ownership and wealth
  segment each split the base into two halves that behave the same way. Mass
  Customer is the biggest wealth band in every age group. Behaviour separates
  these customers, demography does not.

**So what.** Spend the retention budget on the 1,049 customers in the top four
segments (30% of the base, 37% of sales). They are still active and worth
keeping. The 300 in *Lost Customer* generate $738K between them, the smallest
pool in the business, so win-back campaigns aimed there will not pay for
themselves. And since demography doesn't predict value, the 1,000 new prospects
can't be ranked from their profile alone. They need a first purchase before RFM
can say anything.

## Method

| Step | Choice |
|---|---|
| Recency reference | last transaction in the data (Dec 2017), not today |
| Frequency | count of transactions per customer |
| Monetary | **profit**, not revenue: `list_price − standard_cost` |
| Scoring | quartiles 1–4, recency reversed |
| Combination | `100·R + 10·F + M`, so recency carries the most weight |
| Segments | 11 named bands over the combined score |

Recency is measured against the last transaction in the data because the data
ends in December 2017. Against today every customer is equally stale and recency
carries no information. Monetary is margin rather than revenue because a customer
who buys cheap, low-margin stock is worth less than their revenue suggests.

## Segments

| Segment | Customers | Sales |
|---|---|---|
| Platinum Customer | 168 | $1,871,487 |
| Very Loyal | 183 | $1,539,007 |
| Becoming Loyal | 324 | $1,979,138 |
| Recent Customer | 374 | $2,718,022 |
| Potential Customer | 359 | $2,663,869 |
| Late Bloomer | 356 | $1,519,981 |
| Losing Customer | 347 | $3,044,182 |
| High Risk Customer | 368 | $1,927,245 |
| Almost Lost Customer | 316 | $1,821,828 |
| Evasive Customer | 395 | $2,104,348 |
| Lost Customer | 300 | $738,501 |

Best to worst. Read down the list and the behaviour matches the names: recency
climbs and frequency falls as the segments get worse. That is the check that the
cut points produce coherent groups rather than eleven arbitrary slices.

## Limitations

1. **Frequency will not quartile.** Only 14 distinct values across 3,490
   customers, so thousands tie and the four groups come out badly uneven. One
   holds a third of the base, another a sixth. Weakest part of the method.
2. **The combined score is a concatenation, not a magnitude.** A high-value
   customer who drifted away scores 344, a recent one-off buyer who spent almost
   nothing scores 411. Both land in *Recent Customer*.
3. **Frequency and monetary are strongly correlated.** Three measures carry less
   independent information than three measures imply.
4. **One year of transactions.** Someone who bought heavily in 2016 and stopped
   looks identical to someone who never bought at all.
5. **The 11 thresholds are conventional, not derived.** Different cut points give
   different segment sizes from exactly the same customers.

## Dashboards

Two pages in Tableau. The first is above, the second is the customer-level detail
behind it, with nine filters.

![Customer Information dashboard](images/dashboard-2-customer-info.png)

They connect to two sources at different grains, `Customer_Trans_RFM_Analysis.csv`
(3,490 rows, one per customer) and `Transactions_Cleaned.csv` (19,801 rows, one
per transaction), and are deliberately not joined or blended. Joining them
repeats each customer once per transaction and inflates every total.

Workbook: `cutomer-segmentation-dashboard.twbx`.

## Data

`Raw_data.xlsx`: four tables from Sprocket Central Pty Ltd, a bike-parts retailer
trading in NSW, VIC and QLD. 20,000 transactions across 2017, 4,000 existing
customers, 3,999 addresses, 1,000 prospects.

3,490 customers are scored, the ones with at least one surviving transaction
after cleaning. The four DQA notebooks document every problem found and what was
done about it: 179 cancelled transactions removed, 197 product-less transactions
kept with their cost left null, a customer born in 1843 removed, `Unknown` shown
on charts rather than dropped, and four customers with no address kept through a
LEFT join so they survive into the analysis.

## Run it

```bash
pip install pandas numpy matplotlib seaborn openpyxl jupyter
jupyter notebook
```

Run the four `DQA and Data Cleaning *.ipynb` notebooks, then `RFM Analysis.ipynb`,
then open `cutomer-segmentation-dashboard.twbx`.

**Stack:** Python, pandas, NumPy, Matplotlib, seaborn, openpyxl, Jupyter.
Tableau Desktop 2025.1 for the dashboards.

---

*Author: Shruti Pingle*
