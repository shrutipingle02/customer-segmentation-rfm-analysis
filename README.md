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

**Why the reference date is the last transaction.** The data ends in December
2017. Measured against today, every customer is eight years stale and recency
carries no information at all.

**Why monetary is profit.** A customer who spends heavily on low-margin stock is
worth less than their revenue suggests. Segmenting on margin targets the
customers who actually make the business money. Revenue is still carried through
as `total_sales` so the dashboard can report sales — the segmentation and the
reporting are deliberately on different measures.

**Why recency is reversed.** A low recency is good, so its labels run `4,3,2,1`.
Get this backwards and the analysis recommends targeting the customers who left.

**Why recency carries the most weight.** It sits in the hundreds column of the
combined score. In retail, how recently somebody bought is the strongest single
predictor that they will buy again.

## Results

**3,490 customers, $21,927,608 in sales, 11 segments.**

| Segment | Customers | Sales | Share |
|---|---|---|---|
| Losing Customer | 347 | $3,044,182 | 13.9% |
| Recent Customer | 374 | $2,718,022 | 12.4% |
| Potential Customer | 359 | $2,663,869 | 12.1% |
| Evasive Customer | 395 | $2,104,348 | 9.6% |
| Becoming Loyal | 324 | $1,979,138 | 9.0% |
| High Risk Customer | 368 | $1,927,245 | 8.8% |
| **Platinum Customer** | **168** | $1,871,487 | 8.5% |
| Almost Lost Customer | 316 | $1,821,828 | 8.3% |
| Very Loyal | 183 | $1,539,007 | 7.0% |
| Late Bloomer | 356 | $1,519,981 | 6.9% |
| Lost Customer | 300 | $738,501 | 3.4% |

Read down the segment order and the behaviour matches the names: recency climbs
and frequency falls as the segments get worse. That is the check that the cut
points produce coherent groups rather than eleven arbitrary slices.

**What the demographics say about targeting.** Three attributes that look useful
turn out not to be:

- **Gender** — women account for more total purchases only because there are more
  of them. Per customer the two are almost identical.
- **Car ownership** — close to 50% in all three states.
- **Wealth segment** — Mass Customer is the largest band in every age group, in
  both existing customers and prospects. A Platinum customer here is a frequent
  buyer, not necessarily a rich one.

Each splits the base into two halves of similar size and similar behaviour, so
none is a basis for targeting. Behaviour separates the customers, demography does
not.

## Limitations

1. **Frequency will not quartile.** It takes only 14 whole-number values across
   3,490 customers, so thousands tie and `qcut` cannot split them evenly — one
   quartile holds about a third of the base, another a sixth. Boundaries land
   wherever the ties happen to fall. This is the weakest part of the method, and
   it is kept as-is only so the result stays comparable to the standard quartile
   approach.
2. **The combined score is a concatenation, not a magnitude.** A frequent,
   high-value customer who has drifted away scores 344; a recent one-off buyer
   who spent almost nothing scores 411. Both land in *Recent Customer*.
3. **Frequency and monetary are strongly correlated**  buying more times
   generates more margin. The three measures carry less independent information
   than three measures imply.
4. **One year of transactions.** A customer who bought heavily in 2016 and
   stopped looks identical to one who never bought at all.
5. **Segment boundaries are conventional, not derived.** The eleven thresholds
   come from common RFM practice, not from anything in this data. A different set
   of cut points gives different segment sizes from exactly the same customers.

## Dashboards

Two pages in Tableau. The first is above, the second is the customer-level detail
behind it, with nine filters.

![Customer Information dashboard](images/dashboard-2-customer-info.png)

They are not joined or blended. Joining them repeats each customer once per
transaction and every total inflates . every sheet is built on whichever source
has the right grain. Any customer count comes from Customers ,only Top 10
Products and the sparklines use Transactions.

**Dashboard 1 — RFM Analysis.** Two KPIs with sparklines, sales by age, wealth
segment, industry and state, a map of the customer base, top 10 products, the
segment table and an RFM scatter with a parameter that swaps the x-axis between
recency and frequency.

**Dashboard 2 — Customer Information.** The customer-level detail table behind
the summary, with nine filters and navigation buttons both ways.

Workbook: `cutomer-segmentation-dashboard.twbx`. 

## Run it

```bash
pip install pandas numpy matplotlib seaborn openpyxl jupyter
jupyter notebook
```



## Repository contents

```
Raw_data.xlsx                       the four source tables
DQA and Data Cleaning *.ipynb       one notebook per table: problem, reason, fix
RFM Analysis.ipynb                  scoring, segmentation, exploration, export
*_Cleaned.csv                       cleaning outputs
Customer_Trans_RFM_Analysis.csv     3,490 customers x 29 columns — the Tableau feed
cutomer-segmentation-dashboard.twbx packaged workbook, both dashboards
icons/                              navigation and info icons used in the dashboards
```

## Tech stack

Python, pandas, NumPy, Matplotlib, seaborn, openpyxl, Jupyter. 
Tableau Desktop 2025.1 for the dashboards.

---

