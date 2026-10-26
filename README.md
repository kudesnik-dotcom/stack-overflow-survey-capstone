# Developer Technology & Trends Survey — Data Analysis Capstone

An end-to-end analysis of a ~65,000-respondent developer survey (languages, databases, platforms, frameworks, AI-tool adoption, compensation, and demographics), built as the capstone project for the IBM Data Analyst Professional Certificate (Coursera).

**[Live dashboard (Looker Studio)](https://datastudio.google.com/s/rSA70lRVFXg)**

## The pipeline

Five notebooks, each building on the last:

| Stage | Notebook | What it does |
|---|---|---|
| 1. Data Collection | [`01_Data_Collection.ipynb`](01_Data_Collection.ipynb) | Loads the raw survey export (65,457 × 114); scrapes a supplementary programming-language salary benchmark |
| 2. Data Wrangling | [`02_Data_Wrangling.ipynb`](02_Data_Wrangling.ipynb) | Removes duplicates, standardizes category labels, handles missing values deliberately (not blanket-imputed), encodes/scales/engineers features |
| 3. Exploratory Data Analysis | [`03_Exploratory_Data_Analysis.ipynb`](03_Exploratory_Data_Analysis.ipynb) | Job satisfaction, remote work, languages, compensation outliers (including a caught data-entry error), correlations |
| 4. Data Visualization | [`04_Data_Visualization.ipynb`](04_Data_Visualization.ipynb) | Eight polished charts — one per chart type — including the follow-ups promised in stage 3 |
| 5. Dashboard | [`05_Dashboard_Data_Prep.ipynb`](05_Dashboard_Data_Prep.ipynb) | Builds the 12 summary tables behind the Looker Studio dashboard, pushed to Google Sheets |

Each notebook ends with a "Summary & next steps" section pointing at the one after it — read them in order, or jump straight to whichever stage is relevant.

## Data flow

```
data/raw/survey_data_raw.csv  ─────┐
data/raw/language_salaries.csv ────┤→ 02_Data_Wrangling.ipynb
                                    │
                                    ▼
                  data/processed/survey_data_clean.csv  (65,437 × 127)
                                    │
                      ┌─────────────┴─────────────┐
                      ▼                             ▼
         03_Exploratory_Data_Analysis    04_Data_Visualization
                      │                             │
                      └─────────────┬───────────────┘
                                    ▼
                      05_Dashboard_Data_Prep.ipynb
                                    │
                                    ▼
                    Google Sheets → Looker Studio dashboard
```

Notebooks 1–4 all share the same 65,437-row cleaned dataset, so findings carry forward consistently rather than each notebook re-deriving its own version of "clean."

**One exception, noted explicitly where it matters:** `survey_data_updated_clean.xlsx` (the standalone workbook feeding the live dashboard right now) was built from a separate, smaller 18,845-row export rather than the 65,437-row pipeline dataset — both are real and internally consistent, just not the same sample. `05_Dashboard_Data_Prep.ipynb`'s code points at the pipeline's `survey_data_clean.csv`; re-running it there would bring the dashboard onto the same dataset as everything else. This is flagged in the notebook itself, not hidden.

## A few things worth knowing before reading the notebooks

- **Missing data is handled deliberately, not uniformly.** Structural gaps (survey branching logic — e.g. AI-adoption follow-ups only asked of respondents who use AI tools) are left as `NaN` rather than imputed. Low-missingness categorical fields get mode-imputed. High-missingness fields like compensation are left alone rather than letting one filled-in value dominate the analysis.
- **A couple of real bugs got caught and fixed along the way, not papered over:** a mislabeled spreadsheet tab, a column of literally random (`np.random.randint`) placeholder ages that had quietly made it into a live dashboard chart, and dead code in two notebooks that no longer did anything after an earlier change. All are called out at the point they were found, with the fix explained.
- **Three findings are deliberately shown twice, at different levels of polish:** the Rust usage/desire gap, the remote-work split by employment type, and compensation by country each start as a rough exploratory chart in notebook 3, then reappear as the presentation-ready version in notebook 4.

## Repository structure

```
├── 01_Data_Collection.ipynb
├── 02_Data_Wrangling.ipynb
├── 03_Exploratory_Data_Analysis.ipynb
├── 04_Data_Visualization.ipynb
├── 05_Dashboard_Data_Prep.ipynb
├── survey_data_updated_clean.xlsx      # standalone dashboard data snapshot (see note above)
├── data/
│   ├── raw/
│   │   ├── survey_data_raw.csv
│   │   └── language_salaries.csv
│   └── processed/
│       └── survey_data_clean.csv
└── README.md
```

## Tools

Python (pandas, NumPy, Matplotlib, seaborn, scikit-learn), BeautifulSoup, Google Colab, Google Sheets, Looker Studio.
