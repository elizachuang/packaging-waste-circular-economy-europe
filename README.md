# Packaging Waste and the Circular Economy in Europe

**Which countries are furthest from the EU packaging recycling target, and is progress coming from recycling more or from producing less waste?**

[![Packaging waste dashboard](images/dashboard.png)](https://public.tableau.com/views/SustainabilitypackagingwasteandthecirculareconomyinEurope/failingcountries?:language=en-GB&:display_count=n)

*Click the image to open the live dashboard on Tableau Public.*

A learning portfolio project. Data: Eurostat, 2014–2023. Change analysis: 2018 compared with 2023.

---

## Key findings

- **Europe is split in half.** Of the 26 countries with 2023 data, 13 met the 65% recycling target and 13 did not. The EU27 as a whole reached 67.4%, because large countries such as Germany and France pull the total up.
- **Romania (41.0%), Malta (43.4%) and Greece (48.0%) are furthest behind.** Romania and Greece also fell the most between 2018 and 2023 (−16.8 and −15.6 percentage points). Part of that fall may come from a stricter counting method that Eurostat introduced in 2020, not from less recycling.
- **The biggest "improvement" needs a warning.** Iceland rose +26.1 points, but most of that is a change in what Iceland reports, not proof of better recycling.
- **Five countries both recycled more and produced less packaging waste per person (2018 vs 2023):** Norway, Estonia, France, Sweden and Germany (Germany only just: +0.8 points).

---

## Purpose and questions

**Main question:** Which European countries are furthest from the EU recycling targets, and is progress coming from recycling more or from producing less packaging waste?

**Supporting questions:**

1. Which countries are furthest from the 2025 target (65%) in 2023?
2. Which countries improved or got worse between 2018 and 2023?
3. In which country-years did the recycling rate rise while packaging waste per person fell? I call this **"decoupling"**: the two measures move in the good direction at the same time.
4. How many kg per person would each country still need to recycle to reach the target?

---

## Tools

| Tool | What I used it for |
|---|---|
| Google Sheets | Cleaning, joining tables, and KPI calculations |
| Tableau Public | The dashboard |
| Claude Code (Anthropic) | Assistant for documentation and checking |

---

## Data sources

| Source | What it is | How I used it |
|---|---|---|
| [Eurostat `env_waspac`](https://ec.europa.eu/eurostat/databrowser/view/env_waspac/default/table) | Packaging waste by waste management operations. Annual data by country, material, operation and unit. | Main data. Downloaded as CSV from the Eurostat Data Browser on 2026-10-16. |
| [Eurostat `env_waspacr`](https://ec.europa.eu/eurostat/databrowser/view/env_waspacr/default/table) | Recycling rates of packaging waste for monitoring compliance with policy targets. | Used only to cross-check my own calculated recycling rates. |
| Directive 94/62/EC, Article 6(1), as amended by Directive (EU) 2018/852 ([EUR-Lex summary](https://eur-lex.europa.eu/en/legal-content/summary/packaging-and-packaging-waste.html), checked 2026-10-16) | The EU law that sets the packaging recycling targets: 65% by 2025 and 70% by 2030. | Source of the target values in my targets table. |

> **Note on the law:** Regulation (EU) 2025/40 replaces this Directive. I did not review its details, so I use the targets from Directive (EU) 2018/852.

**Coverage in my download**

- **Reporters:** 31 (EU member states, Norway, Iceland, Liechtenstein, and the EU27 total).
- **Years:** `env_waspac` 2014 to 2023. `env_waspacr` 2009 to 2018. From this table I kept only all packaging (`W1501`) and the unit `RT_TGT2025` (the rate as counted for the 2025 target).
- **Material:** only `W1501` (see next section).
- **Unit:** only `KG_HAB`, kg per inhabitant. "Per inhabitant" (also called "per capita" or "per person") means the national amount divided by the number of people living in the country. This makes small and large countries comparable.

---

## What "packaging" means here

`W1501` is the Eurostat code for **total packaging waste, all materials together** (paper and cardboard, plastic, wood, metal, glass and other). It is not plastic only. My data contains no material breakdown, so I make no claims about plastic or any single material.

---

## What I did

1. **Downloaded** two Eurostat tables as CSV. I kept the untouched originals in "raw" tabs, so I can always go back to the source.
2. **Cleaned** them in Google Sheets. I kept only the columns I needed, made the country name appear on every row, turned the year into a number, separated the EU27 total from the individual countries, and checked for blanks.
3. **Understood the numbers before calculating.** Eurostat reports several operations for each country and year: waste generated, recycled, energy recovery, other recovery, and where the recycling happened. I checked the unit (kg per inhabitant) and the observation flags. A flag is a letter Eurostat adds to a value to warn about it, for example:
   - `b` = **break in series**: the way the number is measured changed, so it is not fully comparable with earlier years.
   - `d` = definition differs from the standard.
   - `e` = estimated.
   - `p` = provisional (may still change).
4. **Built a fact table** (one row per country and year) and a small **targets table** with the legal targets and links to the law.
5. **Calculated the KPIs** with formulas (`IFERROR`, `IF` and `XLOOKUP` in Sheets; calculated fields in Tableau). See [docs/methodology.md](docs/methodology.md).
6. **Cross-checked** my recycling rate against Eurostat's published rate (see [Data quality checks](#data-quality-checks-and-limitations)).
7. **Built the dashboard** in Tableau Public.

Column definitions are in [docs/data-dictionary.md](docs/data-dictionary.md).

---

## KPIs

**Recycling rate** = packaging waste recycled ÷ packaging waste generated. **Percentage points (pts)** are the plain difference between two rates: 50% to 61% is +11 points (but +22% in relative terms).

| KPI | Better when | Notes | Status |
|---|---|---|---|
| Packaging waste per person (kg) | Lower | Less waste is the best result. But rich or trade-heavy countries can score high without being worse at managing waste, so compare a country with its own past, not only with other countries. | Built |
| Recycling rate | Higher | The simplest "good" KPI. The rate also rises if waste generated falls while recycling stays flat, so read it together with waste per person. | Built |
| Gap to target (rate minus target) | Higher | Zero or above means the target is met. Negative means still behind. The more negative, the further away. | Built |
| Change in recycling rate (percentage points) | Higher | Positive means improvement. Countries that already recycle a lot have less room to improve, so a small gain at the top is not weak. I compare 2018 with 2023. | Built |
| Kg per person still to recycle to reach the target | Lower | (target − rate) × waste per person. Blank when the country already meets the target. It is kg per person, not total tonnes. | Built |
| Five-year rolling average recycling rate | Higher | Smooths one-off jumps, so it shows where a country typically sits. It says nothing about consistency. For 2023 it covers 2019 to 2023. | Calculated from the fact table; not on the dashboard |
| Decoupling flag | "Yes" is better | Yes = same country, next year, the recycling rate rose and waste per person fell. Partly built into the maths: less waste can raise the rate by itself. | Calculated from the fact table; not on the dashboard |
| Energy recovery share (waste burned for energy) | Depends | Lower is better if you follow the **waste hierarchy** (the EU order of preference: prevent, reuse, recycle, then burn for energy, then landfill). Higher can be good if the alternative was landfill. The share can fall because recycling grew (good) or because more waste went to landfill (bad), so look at total recovery too. | Built in the fact table; not on the dashboard yet |
| Export dependency (recycled outside the EU ÷ total recycled) | Depends | Lower looks safer. Higher is not automatically bad. A high rate with high export dependency may only look good on paper, because Eurostat counts exported waste as recycled. Exports to other EU countries are not in this KPI. | Built in the fact table; not on the dashboard yet |
| Adjusted plastic recycling rate | Higher | Needs the plastic breakdown, which my data does not have. | Planned (needs a new download with materials) |

---

## Analysis and findings

Each finding separates three things:

- **What the data shows**: numbers from my workbook (checked 2026-10-19). The EU27 total is excluded from country rankings.
- **What sources say**: explanations published by the European Commission, the European Environment Agency (EEA), Eurostat and others.
- **My interpretation**: my own reading. Treat it as a hypothesis, not a finding.

### Finding 1: Where countries stand in 2023

**What the data shows**

- 26 countries have 2023 data. 13 met the 65% target and 13 did not.
- The EU27 as a whole reached 67.4%, above 65%. Eurostat marks the EU27 values for 2023 with the flag `i`: "value imputed by Eurostat or other receiving agencies". Imputed means Eurostat filled in part of the total itself. My data does not say why; my guess is that it covers the four countries that had not reported. Either way, the 67.4% is partly an estimate.
- Four countries have no 2023 data: Bulgaria, Croatia, Hungary and Poland. They are not in this comparison.
- Lowest in 2023: Romania 41.0%, Malta 43.4%, Greece 48.0%.
- Highest in 2023: Belgium 79.3%, Iceland 77.3%, Czechia 74.7%. Belgium also has the highest five-year average (about 80%, 2019 to 2023).

**My interpretation**

- Both "the EU met the target" and "half the countries did not" are true. The EU27 figure is driven by large countries such as Germany and France.
- Bulgaria and Hungary were among the lower performers in 2022. So "lowest in 2023" means lowest **among the countries that reported**.
- Iceland's high 2023 rate comes with a warning (see Finding 3).

### Finding 2: Why Malta, Greece and Romania are behind

**What sources say:** causes documented by the European Commission and the EEA. The EEA early-warning assessments (published 2023) use data from 2018 to 2021. They explain the situation at that time. They are not proof of what caused the 2023 numbers.

Some terms used below:

- **Capture rate**: the share of a material that is collected separately for recycling.
- **Producer responsibility scheme** (extended producer responsibility, EPR): companies that put packaging on the market pay for collecting and recycling it.
- **Fee modulation**: producers pay lower fees for packaging that is easier to recycle.
- **Pay-as-you-throw**: households pay according to how much waste they throw away.
- **Deposit return scheme**: you pay a small deposit on a drink container and get it back when you return it.

**Malta** (rate rose about 7.7 points from 2018 to 2023, but is still low)

- Low capture of plastics (about 11%) and metals, "mainly due to the lack of related infrastructure".
- Separate collection not mandatory for companies.
- No advanced fee modulation in the producer responsibility scheme.
- No pay-as-you-throw or packaging tax.
- Only a voluntary deposit scheme (wine bottles).
- A 2017 fire at the national sorting facility.
- Heavy reliance on exports. Eurostat's metadata adds that Malta has no operating domestic plastic recycling facility and measures recycling by exports.
- Recent changes: a deposit scheme for beverage containers (November 2022), regionalised collection (January 2023), mandatory separation (April 2023) and commercial waste gate fees (January 2023).
- Sources: [EEA, Malta](https://www.eea.europa.eu/publications/many-eu-member-states/malta); [Eurostat metadata, Malta](https://ec.europa.eu/eurostat/cache/metadata/EN/env_waspac_esmswap_mt.htm)

*My interpretation:* the 2022–2023 changes may help in later years. My data cannot show this yet.

**Greece** (rate fell about 15.6 points from 2018 to 2023)

- Low capture rates (about 15% for glass and 11% for plastics in the assessment).
- Mandatory separation of non-household packaging delayed.
- Responsibility split between producer organisations and municipalities, with no penalties on municipalities.
- Low public trust that sorted waste is actually recycled.
- A low landfill fee (EUR 20 per tonne) and no landfill ban.
- Possible under-counting of packaging placed on the market.
- Source: [EEA, Greece](https://www.eea.europa.eu/publications/many-eu-member-states/greece)

**Romania** (rate fell about 16.8 points from 2018 to 2023)

- An incentive to under-report, because packaging placed on the market is taxed.
- Traceability software still being developed.
- Packaging data that does not reconcile with municipal waste data.
- Unclear split of duties between municipalities and producer organisations.
- Weak enforcement.
- No advanced fee modulation.
- Low landfill tax.
- A deposit return system for single-use containers only started on 1 October 2022.
- Eurostat's Romania notes mention "better identification of companies placing packaging on the market".
- Sources: [EEA, Romania](https://eea.europa.eu/publications/many-eu-member-states/romania); [Eurostat metadata, Romania](https://ec.europa.eu/eurostat/cache/metadata/EN/env_waspac_esmswap_ro.htm)

*My interpretation (a possibility, not proven):* if more companies are now identified, more packaging is counted as generated. That makes the **denominator** (the "waste generated" number the rate is divided by) larger. So part of Romania's fall may come from counting more packaging, not from recycling less. I cannot prove this from my data.

**A method change affects every country**

**What sources say:** from reference year 2020, Eurostat counts recycling at the input to the final recycling process, not at the output of sorting plants. This gives lower rates and creates a break in the time series ([Eurostat metadata](https://ec.europa.eu/eurostat/cache/metadata/en/env_waspac_esms.htm); [Commission Implementing Decision (EU) 2019/665](https://eur-lex.europa.eu/eli/dec_impl/2019/665)). The EEA estimated the size of the effect for some countries:

| Country | Year | Rate as reported | Rate under the new rules |
|---|---|---|---|
| Greece | 2019 | 60.1% | 52.9% |
| Romania | 2018 | 57.9% | 51.2% |
| Malta | 2019 | 33.7% | 30.3% |

**My interpretation:** my 2018-to-2023 comparison crosses this method change. Part of any fall, especially for Greece, Romania, Finland and Cyprus, may be the stricter counting, not a real decline. The change values in this README should be read with that in mind.

### Finding 3: Who improved most, and the Iceland and Norway caveats

**What the data shows** (change from 2018 to 2023)

| Country | Change in recycling rate (pts) | Change in packaging waste per person |
|---|---|---|
| Iceland | +26.1 | +16% |
| Norway | +11.4 | −12.4% |
| Estonia | +8.1 | −12.0% |
| Malta | +7.7 | +19.5% |
| Italy | +7.6 | +3.0% |
| France | +6.5 | −11.8% |
| Sweden | +4.3 | −5.9% |
| Germany | +0.8 | −5.4% |
| Cyprus | −9.3 | +23.7% |
| Finland | −12.0 | +20.0% |
| Greece | −15.6 | +41.0% |
| Romania | −16.8 | +55.8% |

- 26 countries have data for both 2018 and 2023. Of these, exactly five improved on both measures (recycled more **and** produced less waste per person): Norway, Estonia, France, Sweden and Germany. Germany only marginally (+0.8 points).
- The table shows the five largest gains, the five countries that improved on both measures, and the four largest falls.

**Iceland: mostly a reporting change**

*What sources say:*

- Iceland's Environment Agency notes that from 1 January 2023 the producer responsibility scheme covers glass, metal and wood packaging, and that **backfilled** glass (crushed glass used as filling material in construction) is now counted as recycled.
- The Eurostat metadata lists breaks in the series (for example wood generated +478% and glass recycled abroad +100% versus the previous year) and says backward corrections are likely.
- Real policy change also happened: mandatory separate collection of several fractions started on 1 January 2023, and the EEA expected this to improve recycling from 2023.
- Sources: [EEA, Iceland](https://www.eea.europa.eu/publications/many-eu-member-states/iceland); [Eurostat metadata, Iceland](https://ec.europa.eu/eurostat/cache/metadata/EN/env_waspac_esmswap_is.htm)

*What the data shows:* Iceland is at 42% in 2022 and 77% in 2023. In my raw data, Iceland carries the "definition differs" flag (`d`) for 2020 to 2023.

*My interpretation:* the +26 points is mostly a reporting change. I do not call Iceland "the most improved" country.

**Norway: a real strength, but the gain is not explained**

*What sources say:*

- Norway's deposit return system for single-use plastic bottles and cans is a real strength. Its operator, Infinitum, estimates that almost 99% of those containers are collected ([Investigate Europe](https://www.investigate-europe.eu/posts/deposit-return-the-plastic-recycling-success-opposed-by-southern-europe)).
- Eurostat's Norway notes describe reporting changes in 2022 and 2023: a new wood reporting method; ferrous metal from incineration ash counted under Implementing Decision (EU) 2019/665; new producer organisations starting to report ([Eurostat metadata, Norway](https://ec.europa.eu/eurostat/cache/metadata/EN/env_waspac_esmswap_no.htm)).

*What the data shows:* my raw data flags Norway with a break in series (`b`) for 2022.

*My interpretation:* the producer environmental tax on drink packaging has existed since 1999, and the deposit scheme covers drink containers only. So it explains Norway's high level, not its gain between 2018 and 2023. **The sources I found do not isolate why Norway's rate rose between 2018 and 2023; part of it may be reporting changes.**

**The other improvers: Estonia, France, Sweden, Germany**

Not researched yet (see Next steps). Note: Estonia carries a break flag (`b`) for 2019 in my raw data.

**Finland and Cyprus**

Not researched yet, beyond the 2020 method change described in Finding 2 (see Next steps).

### Finding 4: How much more each country needs to recycle

**What the data shows**

In 2023, the 13 countries below the target would need to recycle this much more packaging waste per person to reach 65% (if waste generated stayed the same):

| Country | 2023 recycling rate | Kg per person still to recycle |
|---|---|---|
| Malta | 43.4% | 37.8 |
| Romania | 41.0% | 30.1 |
| Greece | 48.0% | 18.3 |
| Ireland | 59.0% | 13.5 |
| Finland | 56.7% | 12.8 |
| Lithuania | 60.7% | 6.0 |
| Portugal | 61.8% | 5.8 |
| Cyprus | 60.7% | 4.5 |
| Denmark | 62.7% | 4.3 |
| Norway | 62.8% | 3.2 |
| Latvia | 63.1% | 2.7 |
| Austria | 64.6% | 0.6 |
| Luxembourg | 64.7% | 0.5 |

**My interpretation:** Romania has the lowest rate, but Malta needs the most extra recycling per person, because Malta generates more packaging waste per person (175.6 kg against Romania's 125.4 kg). Austria and Luxembourg are very close to the target.

### Finding 5: Decoupling (recycling rate up, waste per person down, in the same year)

**What the data shows**

- I checked 259 pairs of consecutive years (same country, one year to the next). 62 of them (24%) show decoupling.
- The countries with the most decoupling years: Luxembourg (5); France, Estonia and Germany (4 each).
- In 2023 (compared with 2022), 15 countries decoupled: Czechia, France, Germany, Italy, Latvia, Lithuania, Luxembourg, the Netherlands, Norway, Portugal, Romania, Slovakia, Slovenia, Spain and Sweden.
- These counts are calculated from the fact table, using the definition in [docs/methodology.md](docs/methodology.md). Decoupling is not shown on the dashboard.

**My interpretation:** a single decoupling year is weak evidence. As the KPI table says, less waste raises the rate by itself. A country that decouples in many years (such as Luxembourg, France, Estonia or Germany) is a stronger signal than one good year.

---

## Data quality checks and limitations

### Checks I ran

- **Cross-check against Eurostat.** I compared my recycling rate (recycling ÷ waste generated) with Eurostat's published rate (`env_waspacr`) for the 155 country-years that overlap (2014 to 2018). The average difference was 0.13 percentage points, 96.1% of rows were within 1.5 points, and the largest difference was 2.7 points. The 155 rows are 31 reporters × 5 years, so they include the EU27 total. Without it, the result is almost the same: 150 rows, average difference 0.13 points, 96.0% within 1.5 points, largest 2.7 points. I re-ran this check on the published sheet.
- **Observation flags.** 165 of the 2,093 raw rows carry a Eurostat flag: `d` 92, `b` 39, `e` 14, `p` 9, `i` 8, `be` 2, `bd` 1. All 8 `i` rows (imputed by Eurostat) are the EU27 total for 2023. My final fact table does not yet carry these flags (see Next steps).

### Fixed along the way

- An early version of my rate formula divided by the wrong column. I found it by comparing my rates with Eurostat's published rate.
- The sign of the gap to target was reversed once (behind showed as positive). I corrected it so negative means "still behind".

### Limitations

- **All packaging only (`W1501`).** No material breakdown, no plastic analysis, no material-specific targets.
- **Kg per inhabitant only.** No total tonnes, so "kg per person still to recycle" is per person, not a national total.
- **One target only.** I compare each country with the overall 65% target. The 2030 target (70%) is not used.
- **Exports count as recycled.** Reported rates include waste exported for recycling, so a high rate does not prove domestic recycling.
- **2023 is incomplete.** 26 countries (4 missing). 2022 is the latest year with all countries.
- **Method change and breaks in series.** The counting method changed from 2020, and some countries have breaks in series (see Findings 2 and 3). Comparisons across 2020 should be read with care.
- **Unweighted averages.** Country averages in my region or trend views are unweighted: each country counts equally, whatever its size.

---

## Dashboard

**[Open the live dashboard on Tableau Public](https://public.tableau.com/views/SustainabilitypackagingwasteandthecirculareconomyinEurope/failingcountries?:language=en-GB&:display_count=n)**

What it shows:

- **Headline tiles:** 13 of 26 countries below the 65% target; the EU27 recycling rate in 2023 (67.4%); 26 countries with 2023 data.
- **Packaging recycle rate, 2023:** every country's rate, grouped into "Meets target" and "Below target".
- **Gap to the target per country:** a map of how far each country is above or below 65%.
- **Recycling vs waste:** a scatter of each country's change in recycling rate (points) against its change in waste per person (%), 2018 to 2023. Four labelled corners: recycles more and produces less (best), recycles more and produces more, recycles less and produces less, recycles less and produces more (worst).
- **Gap to 65%:** extra kg per person each country below the target would need to recycle, largest first.
- **Who improved and who didn't:** change in recycling rate, 2018 to 2023, in percentage points, by country.

The Tableau embed code is saved in [docs/tableau-embed.html](docs/tableau-embed.html). GitHub does not run embed code in a README, so it is there for use on a personal website or GitHub Pages.

---

## Next steps

All of these are **Planned**, not done.

| Step | Why | Status |
|---|---|---|
| Re-download `env_waspac` with all materials and unit T (tonnes), from 2010 | Enables material views, the adjusted plastic rate, real tonnes still needed, and a 2010 baseline | Planned |
| Add the Eurostat observation flag as a column in the fact table and mark break years on the charts | Makes data warnings visible to the reader | Planned |
| Add population data (Eurostat) | To convert between kg per person and tonnes | Planned |
| Re-run the 2018-to-2023 comparison split at the 2020 method change (before and after), or compare 2020 to 2023 only | Avoids mixing the old and new counting methods | Planned |
| Research why Estonia, France, Sweden and Germany reduced waste while recycling more | Fills the "not researched" gap in Finding 3 | Planned |
| Research why Finland and Cyprus fell, beyond the 2020 method change | Same gap, for the countries that slipped | Planned |
| Build a second dashboard for energy recovery share and export dependency | These KPIs are in the fact table but not shown yet | Planned |

---

## Repository structure and how to reproduce

```
packaging-waste-circular-economy-europe/
├── README.md
├── .gitignore
├── data/
│   ├── raw/        Eurostat CSVs, untouched (TODO: add files)
│   └── clean/      cleaned tables exported from Google Sheets (TODO: add files, or rely on the Google Sheet)
├── docs/
│   ├── methodology.md      formulas and calculation steps
│   ├── data-dictionary.md  every column in the final tables
│   └── tableau-embed.html  Tableau embed code (for websites, not the README)
└── images/
    └── dashboard.png       dashboard screenshot
```

**To reproduce:**

1. Download `env_waspac` and `env_waspacr` from the Eurostat Data Browser (links in [Data sources](#data-sources)) as CSV, with the filters listed under "Coverage in my download". Save them in `data/raw/`.
2. Open [my Google Sheet](https://docs.google.com/spreadsheets/d/1-qQ3Yn0c9Sn_sHjXvETQ8XxnvaGpWN-r03ozD3PPOw0/edit?usp=sharing) (view only; use File → Make a copy to edit) or import the CSVs into a new sheet.
3. Follow the cleaning steps in [What I did](#what-i-did) and the formulas in [docs/methodology.md](docs/methodology.md).
4. Connect Tableau Public to the cleaned fact table and targets table, and rebuild the calculated fields listed in the methodology.

Note: Eurostat revises its data. A new download may give slightly different numbers from mine.

---

## About me

I am moving from UX/UI design into data analytics. This is a learning portfolio project.

**Eliza Chu-Yun Huang**

- LinkedIn: [linkedin.com/in/chuyunh](https://www.linkedin.com/in/chuyunh/)
