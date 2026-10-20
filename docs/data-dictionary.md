# Data dictionary

Amounts are kg per person. Rates are decimals (0.65 = 65%).

## Fact table (`fact_packaging` / `tableau_data`)

One row per country and year, 2014–2023 (302 rows).

| Column | Meaning |
|---|---|
| `country_name` | Country, or the EU27 total (left out of rankings) |
| `year` | 2014–2023 |
| `material` | Always `W1501`, all packaging |
| `generated` | Packaging waste generated |
| `recovery` | Total recovered (recycling + energy recovery + other) |
| `energy_recovery` | Burned for energy |
| `recovery - other` | Other recovery |
| `recycling` | Recycled, anywhere |
| `recycled in another EU state` | Recycled in another EU country |
| `recycled in the country` | Recycled at home |
| `recycling outside the EU` | Recycled outside the EU |
| `repair` | Repair (mostly wood) |
| `recycle_rate` | recycling ÷ generated |
| `target_2025` | 0.65 |
| `gap_to_target` | rate − target |
| `energy_recovery_share` | energy recovery ÷ generated |
| `export_dependency` | recycled outside the EU ÷ recycling |
| `kg_per_person_to_target` | Extra kg to reach the target; blank if met |
| `status` | "Meets target" or "Below target" |

Many of the "recycled in/outside" and `repair` cells are blank in Eurostat's data.

## Eurostat rates (`fact_rate`)

Eurostat's published rate, used only for the cross-check. 2009–2018, in percent (65.9 = 65.9%).

Columns: `country_name`, `year`, `waste` (`W1501`), `recycle_rate`.

## Targets (`targets`)

Columns: `material_code`, `material_name`, `target_year`, `target_pct` (0.65 for 2025, 0.70 for 2030), `compare_with`, `legal_reference`, `source_url`, `date_checked` (2026-10-16), `helper_text` (lookup key).

## Eurostat flags

| Flag | Meaning |
|---|---|
| `b` | Break in series: measurement changed |
| `d` | Definition differs |
| `e` | Estimated |
| `p` | Provisional |
| `i` | Imputed by Eurostat (only the EU27 total, 2023) |
| `be`, `bd` | Combinations |
