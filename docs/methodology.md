# Methodology

How each KPI is calculated. All inputs are Eurostat `env_waspac`, material `W1501` (all packaging), unit `KG_HAB` (kg per inhabitant). Rates are stored as decimals (0.65 = 65%).

## 1. Inputs

| Input | Meaning |
|---|---|
| Waste generated | Packaging waste generated, kg per person |
| Recycling | Packaging waste recycled, kg per person |
| Target | 0.65 (65%, 2025 target from Directive 94/62/EC Art. 6(1) as amended by Directive (EU) 2018/852) |

In Google Sheets I built the fact table (`fact_packaging`, one row per country and year) from the cleaned Eurostat data, then added the KPI columns with these formulas (row 2 shown; column letters as in the sheet):

| Column | Sheets formula |
|---|---|
| `recycle_rate` (M) | `=IFERROR(H2/D2,"")` (recycling ÷ generated) |
| `target_2025` (N) | `=XLOOKUP($C2&"\|"&2025, targets!$I:$I, targets!$D:$D, "")` (looks up the target for this material and year in the targets tab) |
| `gap_to_target` (O) | `=IF(OR(M2="",N2=""),"",M2-N2)` |
| `energy_recovery_share` (P) | `=IFERROR(F2/D2, "")` (energy recovery ÷ generated) |
| `export_dependency` (Q) | `=IF(OR(K2="", H2=""), "", K2/H2)` (recycled outside the EU ÷ recycling) |
| `kg_per_person_to_target` (R) | `=IF(AND(ISNUMBER(M2),ISNUMBER(N2),N2>M2),(N2-M2)*D2,"")` |
| `status` | `=IF(M2>=N2,"Meets target","Below target")` (in the `tableau_data` tab) |

## 2. Formulas

| KPI | Formula | Notes |
|---|---|---|
| Recycling rate | recycling ÷ waste generated | Both in kg per inhabitant |
| Gap to target | recycling rate − 0.65 | Negative = still behind |
| Kg per person still to recycle | (0.65 − rate) × waste generated per person | Blank when rate ≥ 0.65 |
| Change in recycling rate (points) | (rate 2023 − rate 2018) × 100 | Percentage points |
| Waste change % | waste 2023 ÷ waste 2018 − 1 | Relative change |
| Five-year rolling average | average of the last five years' rates (for 2023: 2019 to 2023) | Only when all five years exist; otherwise blank |
| Decoupling flag | "Yes" when, for the same country in consecutive years, the rate rose **and** waste per person fell | Otherwise "No" |
| Energy recovery share | energy recovery ÷ waste generated | In the fact table only |
| Export dependency | recycled outside the EU ÷ total recycled | Exports to other EU countries not included |

## 3. Worked example

> **Made-up numbers for illustration only. Not real data for any country.**

Country X, 2023: waste generated = 200 kg per person, recycling = 110 kg per person.
Country X, 2018: waste generated = 180 kg per person, recycling = 90 kg per person.

| Step | Calculation | Result |
|---|---|---|
| Recycling rate 2023 | 110 ÷ 200 | 0.55 (55%) |
| Recycling rate 2018 | 90 ÷ 180 | 0.50 (50%) |
| Gap to target 2023 | 0.55 − 0.65 | −0.10 (10 points behind) |
| Kg per person still to recycle | (0.65 − 0.55) × 200 | 20 kg per person |
| Change in recycling rate | (0.55 − 0.50) × 100 | +5 points |
| Waste change % | 200 ÷ 180 − 1 | +11.1% |
| Decoupling flag (2018 → 2023) | Rate rose, but waste also rose | No* |

\* The real flag compares consecutive years (for example 2022 → 2023), not 2018 → 2023. It is shown here only to illustrate the logic.

Check for "kg per person still to recycle": if Country X recycled 20 kg more (130 kg), its rate would be 130 ÷ 200 = 0.65. Target met.

## 4. Why "points" and not "percent"

A rate going from 50% to 61% is **+11 percentage points** (61 − 50). In relative terms it is **+22%** (11 ÷ 50). I report changes in recycling rate in points, because "+22%" can be misread as the rate itself rising by 22 points.

## 5. Tableau calculated fields

`FIXED` level-of-detail expressions put one value per country on every row, so 2018 and 2023 can be compared side by side.

```
Rate 2018   = { FIXED [country_name] : MAX(IF [year]=2018 THEN [recycle_rate] END) }
Rate 2023   = { FIXED [country_name] : MAX(IF [year]=2023 THEN [recycle_rate] END) }
Waste 2018  = { FIXED [country_name] : MAX(IF [year]=2018 THEN [generated] END) }
Waste 2023  = { FIXED [country_name] : MAX(IF [year]=2023 THEN [generated] END) }
Change pts  = ([Rate 2023] - [Rate 2018]) * 100
Waste change % = [Waste 2023] / [Waste 2018] - 1
```

The names above are descriptive. The field names in my Tableau workbook may differ slightly, but the logic is the same. The five-year rolling average and the decoupling flag are calculated from the fact table and are not on the dashboard.

## 6. Cross-check

My recycling rate was compared with Eurostat's published rate (`env_waspacr`, unit `RT_TGT2025`) for the 155 overlapping country-years (2014 to 2018: 31 reporters × 5 years, including the EU27 total). Mean difference 0.13 points; 96.1% of rows within 1.5 points; largest difference 2.7 points. Without the EU27 total: 150 rows, mean 0.13, 96.0% within 1.5 points, largest 2.7.
