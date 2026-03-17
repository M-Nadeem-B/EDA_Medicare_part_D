# Medicare Part D Drug Spending & Prescribing Patterns -- EDA

![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python)
![Pandas](https://img.shields.io/badge/Pandas-2.x-150458?logo=pandas)
![Seaborn](https://img.shields.io/badge/Seaborn-0.13-blue)
![License](https://img.shields.io/badge/License-Public%20Domain-green)
![Dataset](https://img.shields.io/badge/Records-26.8M-orange)

**Author:** Muhammad Nadeem | PharmD Candidate & Data Science Practitioner  
**Dataset Year:** CY 2023 | **Last Updated:** 2025

---

## Project Overview

This project performs a full end-to-end Exploratory Data Analysis on **26.8 million Medicare Part D prescription records** covering every U.S. prescriber, drug, and spending figure for calendar year 2023.

What makes this project different from a standard EDA is the domain lens. This analysis is built by a pharmacist (That's Me btw) -- which means every statistical finding is interpreted through clinical and pharmacoeconomic reasoning, not just described as a number.

> *"Do Medicare drug prices and spending patterns reflect the structural economics of the U.S. pharmaceutical market -- and what does that mean for patients, payers, and policy?"*

---

## Research Questions

This notebook answers five specific, measurable questions:

| # | Question | Key Finding | Initial Thoughts |
|:--|:--|:--|:--|
| 1 | Which drugs dominate prescription volume? | Chronic disease agents -- statins, antihypertensives, metformin | It will show most common prescribe drugs that is chronic disease drugs |
| 2 | Where does Medicare actually spend its money? | Specialty biologics and GLP-1 agonists -- not high-volume drugs | It may or may not match Prescription volume. But what i think it will match |
| 3 | How large is the brand vs. generic price premium? | Synthroid is the only top-10 drug with meaningful brand retention (~9%) | Obviously we have to see patient burden if there |
| 4 | Which specialties drive volume vs. cost? | Primary care = volume; Oncology/Rheumatology = unit cost | Chronic disease will have high volume while advanced drug types would be more costly |
| 5 | Do 65+ patients face disproportionate cost burdens? | Yes -- especially for oncology biologics with near-100% GE65 cost share | We have Geriatrics here so we'll know about their prescription pattern |

---

## Dataset

| Property | Detail |
|:--|:--|
| **Source** | [CMS -- Medicare Part D Prescribers by Provider and Drug](https://catalog.data.gov/dataset/medicare-part-d-prescribers-by-provider-and-drug-ad73e) |
| **Validation Reference** | [FDA Orange Book -- Products.txt](https://www.fda.gov/drugs/drug-approvals-and-databases/drugsfda-data-files) |
| **License** | U.S. Government Public Domain |
| **Rows** | 26,794,878 |
| **Columns** | 22 |
| **Memory** | ~4.4 GB (raw CSV) |
| **Year** | Calendar Year 2023 |

> **Note:** The raw dataset files are not included in this repository due to size. Download directly from the CMS link above and place in a `Datasets/` folder. If you are wondering how big is this file, its >3GB csv file

---

## Project Structure

```
EDA_Medicare_part_D/
│
├── EDA_Medicare.ipynb          # Main analysis notebook
├── README.md                   # This file
└── Datasets/                   # Not included -- download from CMS
    ├── MUP_DPR_RY25_P04_V10_DY23_NPIBN.csv
    └── Products.txt
```

---

## Notebook Structure

```
1. Environment Setup
   └── Libraries, display config, visual theme constants

2. Data Loading
   └── Explicit dtype handling (Prscrbr_State_FIPS as str)

3. Data Understanding
   ├── 3.1 Schema Overview (df.info)
   ├── 3.2 Data Dictionary (22 columns defined)
   └── 3.3 Descriptive Statistics + Skewness Analysis

4. Data Quality Assessment
   ├── 4.1 Missing Values (with CMS suppression policy explanation)
   ├── 4.2 Duplicate Records (brand-splitting pattern identified)
   └── 4.3 Drug Name Validation vs. FDA Orange Book (+ rapidfuzz fuzzy matching)

5. Feature Engineering
   └── Avg_Cost_Per_Claim, Avg_Cost_Per_Day, Avg_Cost_Per_Bene,
       Avg_Days_Per_Claim, Is_Generic_Fill

Chapter 1 -- Drug Prescription Volume
   ├── Top 10 drugs by total claims
   ├── Generic vs. Brand fill rate (pie chart)
   └── Brand market share stratification by generic

Chapter 2 -- Pharmacoeconomics: Cost Analysis
   ├── Top 10 drugs by total Medicare spending
   ├── Volume vs. Cost divergence analysis
   ├── Top 15 drugs by average cost per claim
   └── Brand vs. Generic price premium table

Chapter 3 -- Prescriber Specialty Analysis
   ├── Top specialties by volume vs. spending (side-by-side)
   └── Specialties with highest cost-per-claim

Chapter 4 -- Geographic Spending Analysis
   ├── Top 15 states by total drug spending
   └── Top 15 states by cost per beneficiary

Chapter 5 -- Elderly Population Analysis (GE65)
   ├── GE65 share of total Medicare spend
   └── Drugs with highest elderly cost burden (+ clinical interpretation)

Chapter 6 -- Correlation Analysis
   └── Heatmap of 8 prescribing & cost metrics with full interpretation

Conclusion & Policy Implications
```

---

## Key Findings

### 1. Chronic Disease Dominates Volume
All top-10 drugs by prescription count treat chronic lifetime conditions -- dyslipidemia, hypertension, hypothyroidism, diabetes, and GERD. Atorvastatin alone accounts for **67.6 million fills**. This reflects Medicare's predominantly elderly, multi-morbid beneficiary population.

### 2. The Cost-Volume Paradox
The top-10 drugs by **total spending** are entirely different from the top-10 by **volume**. Specialty biologics and GLP-1 agonists cost over $1,000 per claim vs. under $20 for generic statins. Medicare spending cannot be reduced by managing prescription counts alone.

### 3. Generic Substitution Is Working
Over 95% of all Medicare prescriptions are filled as generics -- confirming the effectiveness of DAW (Dispense As Written) substitution law. The single exception is **levothyroxine (Synthroid)**, which retains ~9% brand fill rate due to its narrow therapeutic index.

### 4. Specialty Drugs Are Effectively Medicare-Exclusive
Oncology biologics like erdafitinib, carfilzomib, and luspatercept show **100% GE65 cost share** -- their patient populations exist almost entirely within the 65+ cohort, consistent with age-related incidence patterns for bladder cancer, myeloma, and MDS.

### 5. Unit Price and Volume Are Statistically Independent
The correlation between `Avg_Cost_Per_Claim` and `Tot_Clms` is **r = -0.02** -- essentially zero. This decoupling confirms a structural two-tier drug economy and has direct implications for formulary design and PBM cost containment strategy. Essentially, this means that rising claim numbers do not correlate with lower costs per claim, requiring, therefore, targeted, two-tiered management strategies.

---

## Technical Highlights

- **26.8M row dataset** processed efficiently with explicit dtype specification and chunked-aware operations
- **FDA Orange Book cross-validation** using set-based O(1) lookups for drug name integrity checks
- **rapidfuzz fuzzy matching** to demonstrate misspelling correction capability
- **CMS suppression policy** correctly interpreted -- null values in `Tot_Benes` and `GE65_*` columns treated as privacy flags, not missing data
- **Brand-splitting data model** identified and explained -- 1.29M duplicate (NPI, Generic) pairs correctly attributed to multi-brand dispensing, not data errors
- **The power law** across all core metrics with log-scale visualization showed Exponential Distribution
- **Median used as primary statistic** throughout -- justified by skewness coefficients

---

## Skills Demonstrated

| Category | Tools & Concepts |
|:--|:--|
| **Data Wrangling** | pandas, numpy, explicit dtype handling, groupby aggregations, merge/map operations |
| **Visualization** | seaborn, matplotlib, custom themes, annotated bar charts, heatmaps, log-scale histplots |
| **Statistics** | Skewness, kurtosis, Pearson correlation, percentile analysis |
| **Domain Knowledge** | Medicare Part D structure, DAW law, FIPS codes, CMS suppression policy, brand/generic economics, NTI drugs, specialty biologics |
| **Data Validation** | FDA Orange Book cross-referencing, fuzzy string matching (rapidfuzz) |
| **Communication** | Policy-brief style conclusions, clinical interpretation of statistical findings |

---

## Setup & Usage

```bash
# Clone the repository
git clone https://github.com/M-Nadeem-B/EDA_Medicare_part_D.git
cd EDA_Medicare_part_D

# Install dependencies
pip install pandas numpy matplotlib seaborn scipy rapidfuzz jupyter

# Download dataset
# 1. Go to: https://catalog.data.gov/dataset/medicare-part-d-prescribers-by-provider-and-drug-ad73e
# 2. Download: MUP_DPR_RY25_P04_V10_DY23_NPIBN.csv
# 3. Go to: https://www.fda.gov/drugs/drug-approvals-and-databases/drugsfda-data-files
# 4. Download: Products.txt
# 5. Place both files in a Datasets/ folder

# Launch notebook
jupyter notebook EDA_Medicare.ipynb
```

---

## Policy Implications

- **Formulary Design:** Generic substitution is working for small-molecule drugs. PBM intervention focus should shift to specialty drug tier management and biosimilar substitution.
- **MTM Targeting:** Elderly patients on drugs with GE65 cost share above the overall average are priority candidates for pharmacist-led medication reconciliation programs.
- **State-Level Programs:** States with high cost-per-beneficiary ratios should be prioritized for 340B drug pricing program expansion.
- **Cost Containment:** The r = -0.02 correlation between unit price and volume means utilization management cannot control specialty drug spend -- price negotiation is the only effective lever.

---

## License

Dataset: U.S. Government public domain -- [usa.gov/government-copyright](https://www.usa.gov/government-copyright)  
Analysis code: MIT License

---

*This analysis is for educational and portfolio purposes. All findings are descriptive and should be interpreted in the context of Medicare beneficiary demographics and CMS program design.*
