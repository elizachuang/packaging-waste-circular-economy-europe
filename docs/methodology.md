# Methodology

All numbers are Eurostat `env_waspac`, all packaging (`W1501`), in kg per person. Rates are stored as decimals (0.65 = 65%).

## Formulas

| KPI | Formula |
|---|---|
| Recycling rate | recycling ÷ waste generated |
| Gap to target | rate − 0.65 (negative = behind) |
| Kg per person still to recycle | (0.65 − rate) × waste generated, only when the rate is below 0.65 |
| Change in rate (points) | (rate 2023 − rate 2018) × 100 |
| Waste change % | waste 2023 ÷ waste 2018 − 1 |
| Five-year average | average rate of the last five years (2019–2023 for 2023), only if all five exist |
| Decoupling years | "Yes" when, from one year to the next, the rate went up and waste per person went down |
| Energy recovery share | energy recovery ÷ waste generated |
| Export dependency | recycled outside the EU ÷ total recycled |

## In Google Sheets

Fact table `fact_packaging`, row 2:

| Column | Formula |
|---|---|
| `recycle_rate` | `=IFERROR(H2/D2,"")` |
| `target_2025` | `=XLOOKUP($C2&"\|"&2025, targets!$I:$I, targets!$D:$D, "")` |
| `gap_to_target` | `=IF(OR(M2="",N2=""),"",M2-N2)` |
| `energy_recovery_share` | `=IFERROR(F2/D2, "")` |
| `export_dependency` | `=IF(OR(K2="", H2=""), "", K2/H2)` |
| `kg_per_person_to_target` | `=IF(AND(ISNUMBER(M2),ISNUMBER(N2),N2>M2),(N2-M2)*D2,"")` |
| `status` | `=IF(M2>=N2,"Meets target","Below target")` |

## In Tableau

`FIXED` puts one value per country on every row, so I can compare 2018 and 2023 side by side.

```
Rate 2018  = { FIXED [country_name] : MAX(IF [year]=2018 THEN [recycle_rate] END) }
Rate 2023  = { FIXED [country_name] : MAX(IF [year]=2023 THEN [recycle_rate] END) }
Waste 2018 = { FIXED [country_name] : MAX(IF [year]=2018 THEN [generated] END) }
Waste 2023 = { FIXED [country_name] : MAX(IF [year]=2023 THEN [generated] END) }
Change pts = ([Rate 2023] - [Rate 2018]) * 100
Waste change % = [Waste 2023] / [Waste 2018] - 1
```

Field names may differ slightly in my workbook. The logic is the same.

## Example (made-up numbers)

A country produces 200 kg of packaging waste per person and recycles 110 kg.

- Rate: 110 ÷ 200 = 55%
- Gap: 55% − 65% = −10 points
- Still to recycle: 0.10 × 200 = 20 kg per person (130 ÷ 200 = 65%)

If the rate was 50% in 2018, the change is +5 points, which is +10% in relative terms. That's why I use points.

## Dashboard 2 formulas

Tableau calculated fields. Field names in square brackets are the columns in my data source. `[recovery - other]` is one field name, not a subtraction.

| Field | Formula |
|---|---|
| Recycled share | `[recycling] / [generated]` |
| Energy recovery share of waste | `[energy_recovery] / [generated]` |
| Other recovery share | `[recovery - other] / [generated]` |
| Not recovered share | `([generated] - [recovery]) / [generated]` |
| Recycled in country | `[recycled in the country] / [recycling]` |
| Recycled in another EU state | `[recycled in another EU state] / [recycling]` |
| Recycled outside the EU | `[recycling outside the EU] / [recycling]` |
| Share abroad | `([recycled in another EU state] + [recycling outside the EU]) / [recycling]` |

**Headline numbers**

- **KPI 1 (not recovered, EU27, 2023):** average of `([generated] - [recovery]) / [generated]` where year = 2023 and the country name contains "European Union".
- **KPI 2 (countries recycling over half abroad):** distinct count of countries where year = 2023 and `[Share abroad]` > 0.5. EU27 excluded.
- **KPI 3 (countries recycling over half outside the EU):** the same, using `[Export Dependency]` > 0.5. `[Export Dependency]` and `[Recycled outside the EU]` hold the same values: both are recycling outside the EU ÷ recycling.

**Checks (2023)**

- recovery = recycling + energy recovery + other recovery
- recycling = in the country + in another EU state + outside the EU

Both hold within rounding.

The 50% threshold is my choice, not an EU rule.
