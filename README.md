# 🎬 OTT Streaming Content Trend Analysis (Netflix)

An end-to-end exploratory data analysis (EDA) of Netflix's content catalog, framed the way a **content acquisition / strategy team** would use it: *what is the catalog made of, how has it changed, and what should we greenlight next?*

![Python](https://img.shields.io/badge/Python-3.10+-blue) ![Pandas](https://img.shields.io/badge/Pandas-EDA-150458) ![SQL](https://img.shields.io/badge/SQL-SQLite-lightgrey) ![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## 📌 Project Overview

| | |
|---|---|
| **Dataset** | [Netflix Movies and TV Shows (Kaggle)](https://www.kaggle.com/datasets/shivamb/netflix-shows) – `netflix_titles.csv` |
| **Size** | 8,807 titles × 12 columns (6,131 movies, 2,676 TV shows) |
| **Period** | Titles added to Netflix from 2008 to September 2021 |
| **Tools** | Python (Pandas, NumPy, Matplotlib, Seaborn), SQL (SQLite), Excel |
| **Deliverables** | EDA notebook (10 visualizations) · Content strategy memo (3 insights) · SQL queries |

### Business questions
1. How has the catalog's **growth and movie/TV mix** changed over time?
2. Which **genres** dominate, and how has the genre mix shifted?
3. What does the **regional (country) mix** look like, and is Netflix becoming more global?
4. How have **content length** (movie minutes, TV seasons) and **maturity ratings** trended?

---

## 📁 Repository Structure

```
├── data/
│   └── netflix_titles.csv
├── notebooks/
│   └── netflix_eda.ipynb          # Full EDA with 10 visualizations
├── sql/
│   └── queries.sql                # 8 core aggregation queries (SQLite)
├── images/                        # Exported charts
├── reports/
│   └── Netflix_Content_Strategy_Report.docx
├── analysis.py                    # Script version of the notebook
└── README.md
```

---

## 🧹 Data Cleaning

| Issue | Fix |
|---|---|
| `date_added` stored as text (`"September 25, 2021"`) | Parsed to datetime, derived `year_added` |
| 3 rows had `duration` shifted into the `rating` column | Moved back to `duration`, rating set to `Unknown` |
| `country` missing for ~9.4% of titles | Filled with `Unknown` (excluded from country charts) |
| `country` and `listed_in` hold comma-separated lists | Split and exploded for country/genre counts |
| `duration` is text (`"90 min"`, `"2 Seasons"`) | Extracted numeric value; movies = minutes, TV = seasons |

> **Note:** Country and genre counts are *tag counts*: a title with 3 countries counts once for each, so totals exceed 8,807.

---

## 📊 Visualizations (10)

| # | Chart | What it shows |
|---|---|---|
| 1 | Movies vs TV split | Catalog is ~70% movies |
| 2 | Titles added per year | Growth peak in 2019, then decline |
| 3 | Top 10 genres | International Movies, Dramas, Comedies lead |
| 4 | Genre share by era | Dramas/Comedies/Action fading, Documentaries & International TV rising |
| 5 | Maturity ratings | TV-MA + TV-14 = ~61% of catalog |
| 6 | Top 10 countries | US, India, UK, Canada, France |
| 7 | Regional mix over time | Non-US share structurally high |
| 8 | Movie length trend | Average movie shrank from ~115 to ~95 min |
| 9 | TV seasons | 67% of shows have only one season |
| 10 | Content freshness | % of additions released within a year fell from ~82% to ~51% |

![Additions by year](images/02_additions_by_year.png)
![Genre share by era](images/04_genre_by_era.png)

---

## 🔑 Key Findings

1. **Growth has peaked.** Annual additions rose from 429 (2016) to 2,016 (2019), then fell to 1,879 (2020). 2021 is a partial year (data ends September).
2. **The catalog is global.** Titles come from 122 countries; the US appears in only ~42% of titles. India (1,046), UK (806) and Canada (445) follow.
3. **Fewer brand-new titles.** Share of additions released within 1 year of being added fell from ~82% (2013–15) to ~51% (2021): more of the library is older/licensed content.
4. **Genre mix is shifting.** Dramas fell from 17.8% to 11.3% of titles (pre-2000 vs 2016–21 releases), while Documentaries (2.3% → 5.4%) and International TV Shows (0.5% → 8.3%) grew.
5. **Different countries, different formats.** India is 92% movies; South Korea (74%) and Japan (63%) are TV-led (K-dramas, anime).
6. **Shorter formats win.** Average movie length fell ~115 → ~95 min; 67% of TV shows have one season and 83% have two or fewer.

---

## 📝 Content Strategy Memo: What to Greenlight Next

| # | Insight | Recommendation |
|---|---|---|
| 1 | Volume has plateaued and freshness is falling | Move from "more titles" to "more new, high-impact titles"; set a freshness target for new additions |
| 2 | International TV is the fastest-growing segment; India is movie-led, Korea/Japan series-led | Double down on **Korean/Japanese series** and **Indian movies** rather than treating "international" as one bucket |
| 3 | Content is getting shorter and mostly single-season; kids/family is only ~10% of catalog | Greenlight **limited series and ~90-min films**, and fill the **family/kids** gap |

Full write-up with charts: [`reports/Netflix_Content_Strategy_Report.docx`](reports/Netflix_Content_Strategy_Report.docx)

---

## 🗄️ SQL

All core aggregations are in [`sql/queries.sql`](sql/queries.sql) and validated against the Pandas results (e.g. genre split via recursive CTE, country split, yearly additions, movie length by era, freshness).

```sql
-- Titles added per year by type
SELECT CAST(SUBSTR(TRIM(date_added), -4) AS INT) AS year_added, type, COUNT(*) AS titles
FROM netflix_titles
WHERE date_added IS NOT NULL
GROUP BY year_added, type
ORDER BY year_added, type;
```

---

## ▶️ How to Run

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
pip install pandas numpy matplotlib seaborn jupyter
jupyter notebook notebooks/netflix_eda.ipynb
```

To test the SQL: load the CSV into SQLite (`pandas.DataFrame.to_sql("netflix_titles", conn)`) and run `sql/queries.sql`.

> Keep the CSV path in the notebook consistent with your folder layout (e.g. `data/netflix_titles.csv`).

---

## ⚠️ Limitations

- Catalog snapshot only: **no viewership, hours-watched or revenue data**, so "popular" means "well represented", not "watched".
- Dataset ends September 2021; 2021 is a partial year.
- The `rating` column is the **maturity rating** (TV-MA, PG-13…), not a user score.
- Netflix catalogs differ by region; this reflects a single global listing.

## 🚀 Next Steps
- Join IMDb ratings/votes to measure quality vs. genre
- Compare with Amazon Prime and Disney+ Hotstar catalogs
- Build an interactive dashboard (Power BI / Tableau / Plotly)

---

⭐ If you found this useful, consider starring the repo!
