# Data dictionary

Columns in my final tables, taken from the Google Sheets workbook. All amounts are kg per person (Eurostat unit `KG_HAB`). Rates and shares are decimals (0.65 = 65%).

## Fact table (`fact_packaging`, exported as `tableau_data`)

One row per country and year: 31 reporters × 2014–2023 (302 rows). Source: Eurostat `env_waspac`, material `W1501`.

| Column | Type | Description |
|---|---|---|
| `country_name` | Text | Reporting country, or the EU27 total ("European Union - 27 countries (from 2020)"). The EU27 total is left out of country rankings. |
| `year` | Number | Reference year, 2014–2023 |
| `material` | Text | Eurostat material code. Always `W1501` (all packaging) in this version |
| `generated` | Number | Packaging waste generated |
| `recovery` | Number | Total recovered (recycling plus energy recovery plus other recovery) |
| `energy_recovery` | Number | Packaging waste burned to produce energy |
| `recovery - other` | Number | Other recovery |
| `recycling` | Number | Packaging waste recycled (in the country, in other EU states and outside the EU) |
| `recycled in another EU state` | Number | Recycled in another EU member state. Blank for many rows |
| `recycled in the country` | Number | Recycled in the reporting country. Blank for many rows |
| `recycling outside the EU` | Number | Recycled outside the EU. Blank for many rows |
| `repair` | Number | Repair (reported for wood). Blank for most rows |
| `recycle_rate` | Decimal | `recycling` ÷ `generated` |
| `target_2025` | Decimal | Legal target for this material, looked up from the targets table (0.65 for `W1501`) |
| `gap_to_target` | Decimal | `recycle_rate` − `target_2025`. Negative = still behind |
| `energy_recovery_share` | Decimal | `energy_recovery` ÷ `generated` |
| `export_dependency` | Decimal | `recycling outside the EU` ÷ `recycling`. Blank when either is missing |
| `kg_per_person_to_target` | Number | (`target_2025` − `recycle_rate`) × `generated`. Blank when the target is met |
| `status` | Text | "Meets target" (rate ≥ 65%) or "Below target". Only in `tableau_data` |

Not in the sheet: the 2018-vs-2023 change (a Tableau calculated field), and the five-year rolling average and decoupling flag (calculated from this table for the README findings). See [methodology.md](methodology.md).

Not yet in the table (Planned): the Eurostat observation flag.

## Published rate table (`fact_rate`)

Eurostat's own recycling rate, used only for the cross-check. Source: `env_waspacr`, material `W1501`, unit `RT_TGT2025`, 2009–2018 (303 rows).

| Column | Type | Description |
|---|---|---|
| `country_name` | Text | Reporting country or EU27 total |
| `year` | Text | Reference year |
| `waste` | Text | Material code (`W1501`) |
| `recycle_rate` | Number | Eurostat's published rate, in percent (65.9 = 65.9%). Note: a percent here, not a decimal as in the fact table |

## Targets table (`targets`)

| Column | Type | Description |
|---|---|---|
| `material_code` | Text | Eurostat material code (for example `W1501` all packaging, `W150102` plastic) |
| `material_name` | Text | Material name |
| `target_year` | Number | 2025 or 2030 |
| `target_pct` | Decimal | Target rate (for `W1501`: 0.65 for 2025, 0.70 for 2030) |
| `compare_with` | Text | Which rate to compare with the target ("reported rate" or "adjusted rate") |
| `legal_reference` | Text | Directive 94/62/EC Art. 6(1)(f)-(i), as amended by Directive (EU) 2018/852 |
| `source_url` | URL | EUR-Lex summary |
| `date_checked` | Date | 2026-10-16 |
| `helper_text` | Text | Lookup key: material code + "\|" + target year (used by `XLOOKUP`) |

## Eurostat observation flags (raw data)

| Flag | Meaning |
|---|---|
| `b` | Break in series: measurement changed, not fully comparable with earlier years |
| `d` | Definition differs |
| `e` | Estimated |
| `p` | Provisional |
| `i` | Value imputed (estimated) by Eurostat or other receiving agencies. In my data, only the EU27 total for 2023 |
| `be`, `bd` | Combinations of the flags above |
