# Olist Customer Segmentation with RFM and K-Means

**Project type:** Individual course project
**Tools:** Python, pandas, NumPy, scikit-learn, Matplotlib, Seaborn, Google Colab

## Project overview

This project analyzes purchasing behavior in the Olist e-commerce dataset and segments customers using Recency, Frequency, and Monetary (RFM) features and K-Means clustering. It connects customer profiles with category purchasing patterns to propose targeted marketing and customer retention strategies.

The saved analysis covers **96,478 delivered orders** and **93,358 unique customers**, with six customer segments. The presentation describes the data period as 2016–2018. RFM metrics use **31 August 2018** as the reference date.

This is an analytical case study. Marketing campaigns, a production recommendation engine, and commercial improvements have not been implemented or measured in the supplied files.

## Business problem and goals

A single marketing strategy can overlook differences in customer spending, purchase frequency, and time since the last purchase. This project aims to:

- Explore order trends and customer purchasing behavior.
- Calculate customer-level RFM features.
- Identify interpretable customer segments with K-Means.
- Profile the size and purchasing behavior of each segment.
- Identify frequently purchased categories within each segment.
- Translate findings into marketing and product recommendation proposals.

## My role

- Prepared and joined six relational CSV tables in Python.
- Inspected missing values and duplicates and filtered for delivered orders.
- Aggregated order items and payments to order level before joining.
- Built customer-level RFM features using `customer_unique_id`.
- Standardized features and evaluated candidate cluster counts using inertia and Silhouette Score.
- Trained a six-cluster K-Means model and interpreted customer profiles.
- Visualized RFM distributions, segment sizes, and category purchasing patterns.
- Presented recommendations for marketing and future recommendation-system development.

## Files

Upload the two supplied files with the following names so these links work:

| File | Contents |
| --- | --- |
| [customer_segmentation.ipynb](customer_segmentation.ipynb) | Python workflow, saved outputs, and charts |
| [customer_segmentation.pdf] | 17-slide presentation in Vietnamese |
| [README.md](README.md) | Project documentation |

## Data inputs

The notebook downloads an Olist folder through `gdown` and reads six CSV files:

| File | Purpose |
| --- | --- |
| `olist_customers_dataset.csv` | Customer identifiers and location |
| `olist_orders_dataset.csv` | Order status and purchase timestamp |
| `olist_order_items_dataset.csv` | Products, item prices, and freight |
| `olist_order_payments_dataset.csv` | Order payment values |
| `olist_products_dataset.csv` | Product category information |
| `product_category_name_translation.csv` | English category names |

The supplied notebook contains the data-download link. Raw CSV files are not included in this repository setup. Access to the download folder must be available to reproduce the analysis.

## Analysis workflow

### 1. Data preparation

Purchase timestamps are converted to datetime, and only `delivered` orders are retained. Item prices and freight are aggregated by `order_id`. Payment records are also aggregated by `order_id` before joining to the order table, avoiding multiplication of payment values from item-level joins.

Two analytical tables are constructed:

- **fact_sales:** order-level data used to calculate RFM.
- **fact_order_detail:** item-level data used to analyze product categories.

### 2. Exploratory analysis

The notebook explores customer-record counts by state, monthly delivered-order volume, RFM distributions, and correlations among RFM features.

The stored frequency output contains **90,557 one-time purchasers**, representing approximately **97.0%** of the 93,358 analyzed customers. Monetary values have a right-skewed distribution.

The state chart uses all records from the customer table, rather than distinct delivered-order customers. São Paulo has 41,746 out of 99,441 customer records, approximately 42.0%. This is a different population from the final RFM cohort.

### 3. RFM feature engineering

| Feature | Definition | Unit |
| --- | --- | --- |
| Recency | Days between 31 August 2018 and the customer's latest purchase timestamp | Days |
| Frequency | Number of distinct delivered orders per customer | Orders |
| Monetary | Sum of order payment values per customer | Brazilian real, R$ |

Customers are grouped by `customer_unique_id`. Monetary represents recorded customer payments, not profit.

### 4. Scaling and clustering

The three RFM features are standardized with `StandardScaler`. K-Means models are evaluated for **k = 2 to 10**, using inertia and Silhouette Score.

The final model uses:

```python
KMeans(n_clusters=6, random_state=42, n_init=10)
```

The presentation identifies k=4 as a candidate from the diagnostics, with a higher Silhouette Score than k=6. Six clusters are retained for business interpretation, including a separate very-high-spending segment. The analysis does not establish k=6 as the statistically optimal solution.

### 5. Segment profiling

The following values come from saved notebook outputs. RFM figures are segment means; percentages are rounded.

| Cluster | Segment | Customers | Share | Recency | Frequency | Monetary (R$) |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| C0 | Recent customers | 33,066 | 35.4% | 87.66 | 1.00 | 125.39 |
| C1 | Dormant customers | 20,431 | 21.9% | 459.42 | 1.00 | 126.30 |
| C2 | Repeat purchasers | 2,778 | 3.0% | 220.97 | 2.11 | 291.81 |
| C3 | High spenders | 4,079 | 4.4% | 227.24 | 1.00 | 712.04 |
| C4 | VIP / very high spenders | 509 | 0.5% | 235.79 | 1.05 | 2,091.18 |
| C5 | Standard customers | 32,495 | 34.8% | 255.98 | 1.00 | 120.52 |

“Recent,” “dormant,” and “VIP” are descriptive labels relative to this dataset and reference date. They do not establish confirmed churn, lifetime value, or long-term loyalty.

### 6. Product category analysis

The notebook ranks categories by the number of item-level rows within each segment. Although the code names the resulting count `orders`, it does not count distinct order IDs.

Top five categories, in descending order from the saved chart:

| Segment | Top categories |
| --- | --- |
| C0 | Health & beauty; bed/bath/table; housewares; sports & leisure; furniture & decor |
| C1 | Bed/bath/table; furniture & decor; sports & leisure; health & beauty; housewares |
| C2 | Bed/bath/table; furniture & decor; sports & leisure; health & beauty; computers & accessories |
| C3 | Watches & gifts; health & beauty; computers & accessories; office furniture; bed/bath/table |
| C4 | Computers & accessories; auto; computers; watches & gifts; office furniture |
| C5 | Bed/bath/table; sports & leisure; health & beauty; computers & accessories; furniture & decor |

These are observed purchase counts, not evidence of preference strength after controlling for overall category popularity.

## Key findings

- Approximately 97% of analyzed customers purchased only once during the observed period, motivating second-purchase experiments.
- C0 and C5 together account for approximately 70.2% of customers and offer a large audience for differentiated follow-up campaigns.
- C1 accounts for 21.9% of customers and has the longest average time since purchase.
- C2 has the highest average purchase frequency, at 2.11 orders.
- C4 is the smallest segment, with 509 customers, but has the highest mean recorded spending, at R$2,091.18.
- High spending does not imply repeat purchasing: C4 averages only 1.05 orders.

## Proposed marketing actions

| Segment | Proposed actions |
| --- | --- |
| C0 | Welcome messages, second-order vouchers, and category-based follow-up |
| C1 | Win-back tests and surveys about reasons for inactivity |
| C2 | Loyalty points, relevant bundles, and cross-selling |
| C3 | Premium bundles, related accessories, and post-purchase follow-up |
| C4 | Priority service, exclusive offers, and high-value customer retention tests |
| C5 | Seasonal offers, shipping incentives, and category-based promotions |

Discount levels and campaign timing in the presentation are proposals. Their effects on conversion and margin require testing.

## Proposed recommendation-system design

The presentation outlines a future system that would:

1. Recalculate customer RFM features when suitable purchase events occur.
2. Apply the fitted scaler and K-Means model to assign a segment.
3. Store the segment code in the customer profile.
4. Use segment-level category rankings as an initial recommendation layer.
5. Refine recommendations using individual purchase history.
6. Monitor feature distributions and refresh the model and category rankings when needed.

This production system is not implemented in the notebook. Customers with no purchase history would require a separate cold-start strategy. Model retraining would also require segment-label alignment because numeric cluster IDs can change.

Suggested evaluation metrics include second-purchase rate, repeat-purchase rate, recommendation conversion, retention, and revenue from existing customers. Percentage improvements shown in the presentation are proposed targets, not achieved outcomes.

## How to run

The original notebook was written for **Google Colab** and uses absolute paths under `/content/Olist/`.

1. Open `customer_segmentation.ipynb` in Google Colab.
2. Run the installation and download cells. Ensure that the linked Drive folder is accessible.
3. Confirm that the six CSV files exist under `/content/Olist/`.
4. Run the cells in order to prepare the data, calculate RFM, train models, and generate charts.

Dependencies used by the notebook:

```text
pandas
numpy
scikit-learn
matplotlib
seaborn
gdown
```

For a local Jupyter environment, install these packages and change the six CSV paths to the local data directory. Exact original dependency versions were not supplied.

The notebook uses `.resample('M')`, and its saved output includes a deprecation warning recommending `.resample('ME')`. Update this expression when using a compatible recent pandas version.

Computing Silhouette Scores on all 93,358 rows for every candidate k can be expensive. A reproducible sample can be used for exploratory evaluation, but sampled scores will differ from the original full-data calculation.


## Presentation coverage

The 17 slides cover: project overview; business context; analytical goals; workflow; geographic EDA; order trends; RFM definitions; Recency and Monetary distributions; Frequency and correlations; cluster-count evaluation; segment sizes; RFM profiles; segment descriptions; category patterns; marketing proposals; recommendation-system proposals; and conclusions.
