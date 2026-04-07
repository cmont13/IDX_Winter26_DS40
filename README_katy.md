# California Property Price Prediction

## Project Overview

**Goal:** Predict `ClosePrice` for single-family residential properties using only features available off-market — no listing price or days on market.

**Property filter:**
- `PropertyType` = Residential
- `PropertySubType` = SingleFamilyResidence

**Target variable:** `ClosePrice`

---

## Dataset

- **Source:** CRMLS monthly sold listings downloaded via FTP
- **Date range:** March 2025 – December 2025
- **Training set:** March – November 2025 (~118,391 rows)
- **Test set:** December 2025 (~10,329 rows) — time-based holdout

**Key features used:**

| Column | Description |
|---|---|
| `LivingArea` | Interior square footage |
| `BedroomsTotal` | Number of bedrooms |
| `BathroomsTotalInteger` | Number of bathrooms |
| `LotSizeSquareFeet` | Total lot size |
| `AssociationFee` | HOA fee |
| `BuildingAreaTotal` | Total building area |
| `Stories` | Number of stories |
| `MainLevelBedrooms` | Bedrooms on main floor |
| `City` | Location (one-hot encoded) |

---

## Preprocessing

1. **Loaded** 10 monthly CSV files and concatenated into a single DataFrame
2. **Filtered** to `PropertyType = Residential` and `PropertySubType = SingleFamilyResidence`
3. **Removed invalid rows** — dropped nulls in `ClosePrice`, duplicates, and logic errors (zero bedrooms, living area < 100 sqft, price ≤ 0)
4. **Outlier removal** — removed top and bottom 0.5% of `ClosePrice` independently within each split to avoid leakage
5. **Train/test split** — time-based: December 2025 held out as test set, January–November 2025 used for training
6. **Encoded categoricals** — `City` one-hot encoded; rare cities with fewer than 100 training examples grouped into `"Other"`
7. **Imputed** missing numeric values using median imputation (fit on train only, applied to test)

---

## Results

| Model | R² | MAPE |
|---|---|---|
| Random Forest | 0.729 | 23.95% |
| XGBoost | 0.719 | 32.12% |
| Linear Regression | 0.679 | 34.01% |

**Best model: Random Forest** (R² = 0.729, MAPE = 23.95%)
