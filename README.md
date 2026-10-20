# Olist E-Commerce Analysis — Customer, Delivery & Sales Analytics

## Executive Summary

This notebook analyzes the public Brazilian Olist e-commerce dataset using Python, SQLite, SQL, and visualizations. The current notebook filters to delivered orders with a recorded delivery date and reports 96,470 such orders from September 2016 through August 2018.

The project covers sales and payment patterns, delivery time, review scores, order timing, and an illustrative forecast. A review of the saved SQL found that the category and seller-state payment totals can be multiplied by joining order-level payments directly to item-level rows. Those rankings are not used as findings below until the query grain is corrected and the notebook is rerun.

## Business Problem

An online marketplace needs to understand delivery performance and customer feedback, track payment value over time, and identify areas for operational follow-up. This project provides descriptive evidence for investigating delivery delays and customer experience; it does not estimate the causal effect of faster delivery or validate the revenue forecast for business use.

Questions explored:

- How does recorded payment value vary by month?
- Which customer states have longer delivery times?
- How do review scores vary across delivery-speed groups?
- When are delivered orders purchased?
- How well does a simple time-trend model predict later monthly payment values?

## Methodology

The notebook loads the Olist order, order-item, payment, review, product, customer, seller, geolocation, and category-translation files. It converts date fields, filters to delivered orders with non-null delivery timestamps, derives delivery time and purchase-time features, and runs SQL queries in an in-memory SQLite database.

| Metric | Definition used |
|---|---|
| Delivered order count | Orders with `order_status = 'delivered'` and a non-null customer delivery timestamp |
| Delivery days | Calendar-day difference between purchase timestamp and customer delivery timestamp |
| Delivery-speed group | Fast: ≤7 days; Normal: 8–14; Slow: 15–21; Very Slow: >21 |
| Review score by delivery group | Average recorded review score after joining reviews to the filtered order table; descriptive association |
| Monthly payment value | Sum of recorded `payment_value` by purchase month for the delivered-order table; this is payment value, not product-only revenue |
| Forecast target | Monthly payment-value series; polynomial trend trained on 16 months and evaluated on the final 3 months |

**Metric integrity note:** The saved category and seller-state queries join order-level payment records to item-level records. An order with multiple items can repeat the same payment amount in that join. The category and seller-state monetary totals therefore require correction and rerun before they are suitable for decisions or headline claims.

## Skills

- **Python:** Pandas, NumPy, Matplotlib, Seaborn, scikit-learn
- **SQL:** SQLite joins, aggregations, grouped analysis
- **Data preparation:** timestamp parsing, delivered-order filtering, derived delivery measures
- **Model evaluation:** chronological holdout, MAE, RMSE, and R²
- **Communication:** descriptive findings, metric definitions, and limitations

## Results & Business Recommendation

| Evidence from the saved notebook | Practical follow-up |
|---|---|
| Average delivery time is 12.1 days; the median is 10 days across the filtered delivered orders. | Use the distribution and state-level breakdown to identify where logistics teams should investigate delays. |
| Amazonas has the longest average delivery time among the states with more than 100 delivered orders: 26.0 days. | Review carrier, route, distance, and order-mix data before choosing an intervention. |
| Average review score is 4.41 for orders delivered in ≤7 days and 3.01 for orders taking >21 days. | Treat this as an association. The analysis does not control for product, seller, customer, or other experience factors and cannot show that delivery speed caused the rating difference. |
| November 2017 has the highest monthly payment value in the period shown: R$1,153,393. | Review the underlying order and payment mix. The notebook does not isolate a Black Friday effect. |
| The polynomial model has a held-out R² of −14.394, MAE of R$185,932, and RMSE of R$203,858 on the final three observed months. | Do not use the illustrative forecast for planning. Establish a seasonal baseline and evaluate a time-series model on multiple rolling windows first. |

The saved notebook's category and seller-state totals are intentionally omitted from the findings until their payment-to-item join is corrected and the results are recomputed.

## Next Steps

1. Rewrite category sales using item-level product prices, with a clear decision on whether freight is included.
2. Rewrite seller-state sales at item grain and calculate order averages from an order-state aggregation.
3. Rerun the notebook with the source CSV files and refresh the category/seller charts and reported outputs.
4. Compare review scores at one row per order or document how multiple reviews are handled.
5. Compare the forecast with a seasonal-naive baseline and rolling time-based validation.

### Run the notebook

1. Clone this repository and install the packages in `requirements.txt`.
2. Download the Olist CSV files from the [public dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) into `data/`.
3. Start Jupyter and run `notebooks/olist_analysis.ipynb` from top to bottom.

The raw data is excluded from Git; the notebook and visualizations are included.

**Author:** Salahuddin K M · [Portfolio](https://erskm.github.io) · [GitHub](https://github.com/ErSKM)
