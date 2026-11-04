# Finnish Municipality Data Construction with MATLAB

**Data preparation and integration for a group project on unemployment patterns across Finnish municipalities**

This repository documents my **data-construction contribution** to a group university assignment. The wider team project examined municipal characteristics in relation to unemployment; **my work focused on organizing and preparing the analytical dataset**, not on developing the team's clustering or prediction models.

## Project overview

- **Data source:** Statistics Finland municipal indicators and enterprise-establishment statistics, supplied for this workflow as preprocessed Excel files.
- **Tools:** MATLAB Live Script, Excel / Power Query (upstream preprocessing documented in the Live Script).
- **Unit of analysis:** Municipality × year.
- **Final coverage:** **308 municipalities**, **2018–2023**, **1,847 rows**, **22 columns**.
- **Deliverable:** A structured, quality-checked dataset for subsequent statistical analysis and modeling by the group.

## My contribution: data-construction workflow

The process is documented in [`matlab/Data_construction.mlx`](matlab/Data_construction.mlx).

1. **Inspect inputs.** Read the preprocessed `Establishments.xlsx` and `Municipal_key_figures.xlsx` tables; inspect variables, years, and municipality identifiers.
2. **Filter municipality records.** Retain `RegionCode` values beginning with `KU`, which distinguish municipality records from other regional aggregations; align available years.
3. **Reshape indicators.** Convert the municipal key-figures table from long format to one row per municipality-year using MATLAB `unstack`.
4. **Integrate sources.** Join the two sources on the combination of `RegionCode` and `Year` using `innerjoin`.
5. **Standardize variables and handle missingness.** Rename lengthy indicator fields, investigate missing observations, exclude 2024 because several workplace indicators are unavailable for that year, and remove the remaining incomplete rows.
6. **Prepare features.** Define `UnemploymentRate` as the target for the group's later supervised analysis; review candidate predictors and correlations, then remove selected redundant predictors from the final feature set.
7. **Check data quality.** Inspect correlation patterns and outliers; retain valid municipal differences instead of automatically deleting extreme observations.
8. **Export a reusable dataset.** Save the final municipality-year table as CSV and Excel for subsequent group analysis.

The original downloaded Statistics Finland tables had already undergone some cleaning and restructuring in Excel/Power Query **before** these two input workbooks were used in MATLAB. This repository documents the MATLAB stage, not the complete upstream extraction process.

## Output dataset

[`data/processed/Municipal_key_figures_final.csv`](data/processed/Municipal_key_figures_final.csv) has:

| Property | Value |
|---|---:|
| Municipality-year records | 1,847 |
| Distinct municipalities | 308 |
| Years | 2018–2023 |
| Columns | 22 |
| Empty cells in final CSV | 0 |
| Duplicate municipality-year keys | 0 |

There are **307 observations in 2018** and **308 observations in each of 2019–2023**. Thus, the final dataset is close to, but not perfectly, balanced across municipalities and years.

The 22 columns include:

- **Identifiers:** `MunicipalityCode`, `Municipality`, `Year`.
- **Demographics and housing:** e.g. `UrbanShare`, `DependencyRatio`, `OlderShare64`, `YouthShare15`, `ForeignCitizens`, `RentalHousingShare`.
- **Education and labor market:** e.g. `TertiaryShare`, `UpperSecondaryShare`, `EmploymentRate`, `WorkInOwnArea`, `WorkplaceSelfSufficiency`.
- **Migration and economy:** e.g. `NetMigration`, `PopulationChange`, `Establishments`, `TurnoverPerPerson`, and sectoral employment shares.
- **Target prepared for downstream analysis:** `UnemploymentRate`.

The target and predictors were **prepared for the team**; this repository does not claim that I trained or evaluated the group's unemployment-prediction models.

## Repository structure

```text
finland-municipality-data-construction/
├── README.md
├── matlab/
│   └── Data_construction.mlx
└── data/
    ├── input/
    │   ├── Establishments.xlsx
    │   └── Municipal_key_figures.xlsx
    └── processed/
        ├── Municipal_key_figures_final.csv
        └── Municipal_key_figures_final.xlsx
```

## Reproducing the MATLAB data preparation

1. Download or clone this repository and open `matlab/Data_construction.mlx` in MATLAB.
2. Make the two Excel inputs accessible to MATLAB. **The Live Script currently uses filenames without folder paths** (`readtable("Establishments.xlsx")`, etc.), so either put the files in MATLAB's current working folder or update those two `readtable` paths to `data/input/...`.
3. Run the Live Script sections in order. It reshapes, joins, cleans, checks, and exports the final table.
4. **Output paths are also relative** in the current script; move the exported files into `data/processed/` or update the two `writetable` paths accordingly.
5. Compare the result against the included final CSV, particularly its 1,847 rows, 22 columns, year coverage, and missing-value checks.

**Reproducibility note:** The supplied Excel inputs are already preprocessed versions of the source data. Reproducing every initial Excel/Power Query step from raw Statistics Finland downloads requires the original extraction queries and preprocessing history. I have not independently rerun the MATLAB Live Script in this repository.

## Data interpretation and limitations

- Observations are **municipality-years**, not individuals. The dataset supports analysis of municipal patterns, not individual-level conclusions.
- The merged and cleaned output does not represent every original regional aggregate or every municipality-year present in the initial workbooks.
- Some predictors are correlated; the documented variable-selection step makes choices for the **downstream group analysis**, not proof of causal relationships.
- Unusually large or small municipal values can reflect legitimate structural differences between municipalities and were not automatically removed.
- The project is **data engineering / analytical preparation**, not a published forecasting model or a validated prediction service.

## Source and attribution

The municipal and business indicators originate from **Statistics Finland**: [stat.fi](https://stat.fi/en). See the source datasets and metadata for definitions, time coverage, updates, and reuse terms. The data-construction workflow was prepared for a **group course assignment**; other team members' clustering and predictive modeling work is outside the scope of my claimed contribution here.
