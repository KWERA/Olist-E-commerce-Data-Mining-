# Olist E-commerce Data Mining

This project analyses the Brazilian E-Commerce Public Dataset by Olist. It covers 99,441 orders placed between Sep 2016 and Oct 2018, spread over 9 linked tables. All the work is in one notebook, `B35098_Olist_datasets_analysis.ipynb`.

## Tasks

| Task | Brief | Methods used |
|---|---|---|
| 1 | Explore and comment on the data and carry out necessary data preparation, with justification. | EDA, data quality checks, feature engineering, IQR outlier flags |
| 2 | Discover product categories frequently purchased together. How can this information help increase sales? | Association rules (support, confidence, lift), Fisher's exact test, Benjamini-Hochberg correction |
| 3 | Segment sellers by sales and service characteristics. How can Olist position itself to better serve, and thus retain, these segments? | K-means, agglomerative clustering, silhouette, ARI stability |
| 4 | Identify unusual orders that merit operational review, with justification. | Business rules plus Isolation Forest |
| 5 | Develop a model or models to predict whether an order will arrive late, and the number of days that order will take from purchase to delivery. | Logistic and linear regression, histogram gradient boosting, time-based split |

## Key findings

- Late orders average 2.3 stars against 4.3 for on-time orders. Lateness depends mostly on distance and region.
- 97% of customers buy only once. Only 727 orders (0.7%) contain 2 or more categories.
- Three category pairs hold up in both 2017 and 2018 (`bed_bath_table + home_confort`, `baby + cool_stuff`, `baby + toys`).
- Three seller segments were found. The "core established" group (797 sellers) brings in 82% of revenue.
- 5,164 orders (5.2%) are flagged for review, with a top-500 shortlist for one team.
- The late-delivery model reaches ROC-AUC 0.71. The delivery-days model has a mean error of 3.4 days, about 31% better than a median guess.

## Project structure

```
.
├── B35098_Olist_datasets_analysis.ipynb   # main notebook (Tasks 1 to 5)
├── data/                                  # raw Kaggle CSVs, downloaded by you (not tracked by git)
├── clean/                                 # outputs written by the notebook
├── requirements.txt
├── .gitignore
└── README.md
```

## Data

The raw data is **not included** in this repository. You must download it from Kaggle and place it in the `data/` folder before running the notebook.

**Dataset link.** [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

### Option 1. Manual download

1. Open the dataset link above and sign in to Kaggle.
2. Click **Download** to get the archive (`archive.zip`).
3. Unzip the archive.
4. Move all 9 CSV files into the `data/` folder at the root of this project.

### Option 2. Kaggle command line

This needs a Kaggle API token saved as `~/.kaggle/kaggle.json` (Windows: `C:\Users\<you>\.kaggle\kaggle.json`).

```bash
pip install kaggle
kaggle datasets download -d olistbr/brazilian-ecommerce -p data --unzip
```

### Expected folder structure

After the download, the `data/` folder should look exactly like this. The file names must not be changed, because the notebook loads them by name.

```
data/
├── olist_customers_dataset.csv
├── olist_geolocation_dataset.csv
├── olist_order_items_dataset.csv
├── olist_order_payments_dataset.csv
├── olist_order_reviews_dataset.csv
├── olist_orders_dataset.csv
├── olist_products_dataset.csv
├── olist_sellers_dataset.csv
└── product_category_name_translation.csv
```

If the geolocation file arrives as `olist_geolocation_dataset.csv.zip`, unzip it so that `olist_geolocation_dataset.csv` sits directly in `data/`.

The raw data is excluded from git because of its size (the geolocation table alone has about 1 million rows). The dataset is shared by Olist under the CC BY-NC-SA 4.0 licence.

## Setup

Python 3.11 or later is needed. The results were produced with Python 3.13.5.

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

## Running

```bash
jupyter lab B35098_Olist_datasets_analysis.ipynb
```

Run all cells from top to bottom. The notebook reads from `./data/` and creates `./clean/` on its own. The final "Headline numbers" cell recomputes the main figures quoted in the text, so you can check they still match after any change.

## Outputs

The notebook writes these files to `clean/`.

| File | Content |
|---|---|
| `orders_master.csv` | One row per order with flags and time features (99,441 rows) |
| `order_items_clean.csv` | Items joined with product, seller and seller-to-customer distance |
| `seller_profile.csv` | One row per seller with sales and service measures |
| `basket_items.csv` | One row per order and category, used for Task 2 |
| `geolocation_by_zip.csv` | Cleaned coordinates, one row per zip prefix (19,012 rows) |
| `seller_segments.csv` | Segment assigned to each clustered seller (Task 3) |
| `orders_for_review.csv` | All orders flagged for review, with reasons (Task 4) |
| `orders_for_review_top500.csv` | Priority shortlist of 500 orders (Task 4) |

## Reproducibility

- Random seeds are fixed (`random_state=42`).
- Task 5 uses a time split. Models train on Jan 2017 to May 2018 and are tested on Jun to Aug 2018.
- Only information known at the time of purchase is used as a model input, so there is no leakage.
- No orders are deleted during cleaning. Problems are fixed where the fix is clear and flagged otherwise.

## References

Full references are listed at the end of the notebook. The main ones are Agrawal, Imieliński and Swami (1993) on association rules, Benjamini and Hochberg (1995) on false discovery rate, and Rousseeuw (1987) on silhouettes.

Kwera Stella M. B35098
