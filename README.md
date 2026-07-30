# Layoffs Data Cleaning Project (SQL)

A SQL-based data cleaning project that transforms a messy, real-world layoffs dataset into a clean, analysis-ready table. This project demonstrates practical data cleaning techniques using MySQL — including deduplication with window functions, data standardization, null handling via self-joins, and schema cleanup.

## 📋 Project Overview

Raw datasets pulled from the web are rarely analysis-ready — they contain duplicate records, inconsistent formatting, missing values, and unnecessary columns. This project takes a raw `layoffs` dataset and works through a full cleaning pipeline to produce a trustworthy, query-ready table.

## 🎯 Objectives

1. **Remove duplicates** — identify and eliminate exact duplicate records
2. **Standardize the data** — fix inconsistent text formatting and values
3. **Handle null/blank values** — populate what can be recovered, remove what can't
4. **Remove unnecessary columns** — drop helper columns used only during cleaning

## 🛠️ Tools Used

- MySQL
- Window functions (`ROW_NUMBER() OVER (PARTITION BY ...)`)
- Common Table Expressions (CTEs)
- Self-joins for null backfilling
- `ALTER TABLE`, `STR_TO_DATE`, `TRIM`

## 📊 Dataset

The dataset contains records of company layoffs, including:
- `company`, `location`, `industry`, `country`
- `total_laid_off`, `percentage_laid_off`
- `date`, `stage`, `funds_raised_millions`

> Add your dataset source link here (e.g., Kaggle) if you're including the raw CSV in this repo.

## 🔍 Cleaning Process

### 1. Staging Setup
Copied the raw `layoffs` table into a staging table (`layoffs_staging`) to preserve the original raw data untouched throughout the cleaning process.

### 2. Duplicate Removal
Used `ROW_NUMBER()` partitioned across all relevant columns to flag exact duplicate rows, then removed any row where `row_num > 1` via a second staging table (`layoffs_staging2`), since MySQL doesn't support deleting directly from a CTE.

### 3. Standardization
- Trimmed extra whitespace from company names
- Consolidated inconsistent industry labels (e.g., multiple crypto-related variants → `Crypto`)
- Standardized country name variants (e.g., trailing punctuation) → `United States`
- Converted the `date` column from text to a proper `DATE` type

### 4. Null/Blank Handling
- Converted blank `industry` fields to `NULL` for consistency
- Used a self-join on `company` to backfill missing industry values from other records of the same company
- Removed rows where both `total_laid_off` and `percentage_laid_off` were missing, since no meaningful layoff data could be recovered from them

### 5. Final Cleanup
Dropped the temporary `row_num` helper column, leaving a clean, deduplicated, standardized table ready for analysis.

## 📁 Repository Structure

```
layoffs-data-cleaning/
├── README.md
├── sql/
│   └── data_cleaning.sql
└── data/
    └── layoffs_raw.csv (Data source: layoffs.fyi, via Kaggle (Layoffs 2022 dataset))
```

## ▶️ How to Run

1. Create a database and import the raw dataset into a table named `layoffs`
2. Run `sql/data_cleaning.sql` step by step (recommended) or as a full script
3. The final cleaned table will be `layoffs_staging2`

## 💡 Key Takeaways

- Always clean data in a staging copy, never on the raw table
- Window functions are a reliable way to detect duplicates across multiple columns
- Self-joins are a powerful way to backfill missing categorical data from related rows
- Not all missing data should be filled — some rows are better removed if no reasonable inference is possible

## 📬 Contact
https://www.linkedin.com/in/manas-chatufale/
Feel free to reach out if you have questions or suggestions about this project.
