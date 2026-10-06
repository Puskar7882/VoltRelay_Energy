# VoltRelay Energy: Network Performance Analysis

End to end data analysis of an electric vehicle battery swapping network, built for **[Data Analytics Hackathon '26 BY Gradient Learnings ]**.

**Notebook (Google Colab):** [Open in Colab](https://colab.research.google.com/drive/1qPapjZWOzOgm69rXilYp070uXhCZINi8)

---

## Overview

This project analyzes the operational, technical, financial and customer retention performance of a battery swapping network. It combines swap transactions with station telemetry, rider profiles, battery data, support tickets, city level context, station metadata and fleet partner contracts.

The analysis answers six business questions:

1. How is network performance changing over time?
2. Where and when are swap failures concentrated?
3. What station and geographic characteristics are linked to performance differences?
4. How do batteries and charger equipment affect operations?
5. How do pricing and fleet-partner contracts affect economics?
6. Which operational factors are associated with early rider churn?

## Dataset

The raw data was provided by the hackathon organizers. **The CSV files are not included in this repository** because of their size (3.8M+ rows in the largest file). To rerun the analysis, place the files in `data/raw/` or upload them to Colab.

| File | Rows | Purpose |
|---|---|---|
| `swap_events.csv` | 3,877,013 | Swap attempts, failures, queue time, battery, pricing, transactions |
| `station_hourly_status.csv` | 1,487,712 | Hourly capacity, charging, temperature, outages, telemetry |
| `riders.csv` | 20,000 | Rider profiles, vehicles, plans, signup details, cities |
| `batteries.csv` | 6,500 | Supplier, capacity, SOH, firmware, retirement |
| `support_tickets.csv` | 44,000 | Issue categories, resolution, CSAT |
| `stations.csv` | 152 | Location, charger generation, capacity, connectivity |
| `city_daily_context.csv` | 3,282 | Weather, outages, holidays, events, competition |
| `fleet_partners.csv` | [12] | Partner contracts, discounts, amendments |

## Tech Stack

Python 3.13 · Pandas · NumPy · Matplotlib · Seaborn · Jupyter / Google Colab

## Workflow

```
Raw CSVs -> Load & validate -> Data quality checks -> Cleaning
        -> Feature engineering -> Table joins
        -> Six analyses (performance, failures, stations, batteries, pricing, retention)
        -> Visualizations -> Business recommendations
```

## Data Quality & Cleaning

| Issue | Handling |
|---|---|
| Firmware v3.2.0 timestamp error (Mar 10 to Apr 14, 2025), 139,490 rows | Shifted timestamps by 5h 30m |
| Inconsistent city names (Bengaluru, Bangalore, BLR, MUM, HYD, ...) | Normalized to 6 canonical cities |
| Two test stations (`STN-TST` prefix) | Separated from the clean station view |
| Near-duplicate swap events (same rider and station within 2 min, offline sync) | Flagged 652 likely duplicates |
| Negative `km_since_last_swap` values (7,814) | Set to missing |
| SOC/SOH values above 100% (sensor drift) | Capped at 100 |

**Engineered features:** `is_completed`, `event_date`, `hour_of_day`, `event_month`, `season`, `rider_tenure_days`, `energy_cost_inr`, `contribution_inr`, `soh_degradation`, rider churn indicators, station-level failure rates and partner-level contribution metrics.

## Key Findings

**1. Network performance**
- Swap volume and revenue grow over the period, but failure rates spike every April to June, which the analysis links to extreme-heat periods. The pattern repeats in 2025.
- A pricing change around July 2024 is associated with higher contribution per swap.

**2. Failure patterns**

| Charger generation | Failure rate | | City | Failure rate |
|---|---|---|---|---|
| Gen1 | 7.23% | | Jaipur | 7.85% |
| Gen2 | 4.84% | | Delhi NCR | 7.35% |
| Gen3 | 4.84% | | Hyderabad | 6.69% |
| | | | Pune | 4.99% |
| | | | Bengaluru | 4.56% |
| | | | Mumbai | 4.45% |

Failures also concentrate in evening peak hours.

**3. Stations and equipment**
- Failure rates differ far more by charger generation than by location type.
- Average turnaround time: Gen1 about **91.8 min** vs Gen3 about **42.1 min**.

**4. Batteries**
- Average degradation: Amptek 18.19 pp, Cellora 18.25 pp, **Kyron 35.46 pp**.
- Lower battery health goes with shorter distance between swaps: 44.2 km (SOH below 70%) vs 70.1 km (SOH 90 to 100%).

**5. Pricing and partners**
- Peak tariff has the highest average contribution per completed swap.
- ZipDrop's discount rose from 12% to 28% on Nov 1, 2024, reducing contribution by an estimated ₹6.31 per swap, about **₹21.5 lakh** in the analyzed post amendment period.

**6. Early churn**
- 6,762 riders were observable; 1,443 were early churners (**21.34%**).
- Average personal failure rate in the first 30 days: **7.93%** for churned riders vs **5.37%** for retained riders.

## Recommendations

1. Prioritize replacing Gen1 chargers (higher failures, much slower turnaround).
2. Prepare thermal-management and summer readiness measures before April to June.
3. Review battery supplier performance, especially Kyron.
4. Review high impact partner contracts such as the ZipDrop amendment.
5. Track failed swaps in a rider's first 30 days as a retention metric.

## Visualizations

<!-- Add your chart images to an `images/` folder and uncomment the lines below -->
<!-- ![Failure rate by charger generation](images/failure_by_charger.png) -->
<!-- ![Churn vs personal failure rate](images/churn_vs_failure.png) -->

## Repository Structure

```
VoltRelay_Energy/
├── notebooks/        # Analysis notebook
├── images/           # Chart screenshots used in this README
├── README.md
├── requirements.txt
└── .gitignore        # Excludes the large raw CSV files
```

## How to Run

**Option 1: Google Colab (easiest)**
Open the [Colab notebook](https://colab.research.google.com/drive/1qPapjZWOzOgm69rXilYp070uXhCZINi8), upload the CSV files and run the cells from top to bottom.

**Option 2: Locally**
```bash
git clone https://github.com/Puskar7882/VoltRelay_Energy.git
cd VoltRelay_Energy
pip install -r requirements.txt
jupyter notebook
```
Place the CSV files in `data/raw/`, open the notebook in `notebooks/`, and run all cells. The notebook reads data from `../data/raw`, so run it from inside `notebooks/`.

## Limitations

- This is a descriptive and diagnostic study; observed relationships do not prove causation.
- Business-impact figures (such as the ₹21.5 lakh estimate) come from before/after comparisons, not controlled experiments.
- The churn definition (30 days or less of activity, with 60+ days of follow-up data) was created for this project.
- The analysis uses only the provided hackathon data, which contains known quality issues that were cleaned as described above.

## Author

**Puskar Gayen** · [GitHub](https://github.com/Puskar7882)