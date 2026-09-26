VoltRelay Energy --- Network Performance Analysis

Hackathon project: end-to-end data analysis of an electric vehicle
battery-swapping network.

Overview

VoltRelay Energy --- Network Performance Analysis is a large-scale
exploratory and business analytics project built to understand the
operational, technical, financial, and customer-retention performance of
a battery-swapping network.

The analysis combines swap transactions with station telemetry, rider
profiles, battery information, customer-support tickets, city-level
context, station metadata, and fleet-partner contracts.

The project focuses on identifying:

Network performance trends over time

Swap failure patterns by charger generation, city, station, and time

Station and geographic performance differences

Battery and equipment health patterns

Pricing and partner economics

Early rider-retention/churn drivers

Operational root causes that can be translated into business actions

Project Objectives

The analysis answers six major business questions:

How is network performance changing over time?

Where and when are swap failures concentrated?

What station and geographic characteristics are associated with
performance differences?

How do batteries and charger/equipment characteristics affect
operations?

How do pricing structures and fleet-partner contracts affect
economics?

What operational factors are associated with early rider churn?

Dataset

The notebook works with eight related datasets:

Dataset                                               Rows Purpose

swap_events.csv                                3,877,013 Swap attempts,
failures, queue time,
battery, pricing and
transaction
information

station_hourly_status.csv                      1,487,712 Hourly station
capacity, charging,
temperature, outages
and telemetry

riders.csv                                        20,000 Rider profiles,
vehicles, plans,
signup information
and cities

batteries.csv                                      6,500 Battery supplier,
capacity, SOH,
firmware and
retirement
information

support_tickets.csv                               44,000 Rider issues, ticket
categories,
resolution and CSAT
information

stations.csv                                         152 Station location,
charger generation,
capacity,
connectivity and
commercial
information

city_daily_context.csv                             3,282 Weather, outages,
holidays, events and
competitive context

The notebook also performs dataset row-count validation before analysis.

Tech Stack

Python 3.13.7

Pandas --- data manipulation and aggregation

NumPy --- numerical operations

Matplotlib --- visualization

Seaborn --- statistical/data visualization

Jupyter Notebook --- interactive analysis

Analysis Workflow

Raw CSV datasets
       │
       ▼
Data loading & validation
       │
       ▼
Data understanding
       │
       ▼
Data quality checks
       │
       ▼
Data cleaning
       │
       ▼
Feature engineering
       │
       ▼
Dataset joins / enrichment
       │
       ├── Network performance
       ├── Failure analysis
       ├── Station & geographic analysis
       ├── Battery/equipment analysis
       ├── Pricing & partner economics
       └── Rider retention analysis
       │
       ▼
Visualizations & findings
       │
       ▼
Business recommendations

Data Quality & Cleaning

The notebook performs several practical data-quality checks before
drawing conclusions.

Timestamp correction

A firmware-specific timestamp issue was identified for v3.2.0 events
between March 10 and April 14, 2025.

139,490 rows were affected.

The timestamps were shifted by 5 hours 30 minutes during
cleaning.

City normalization

Rider city values contained multiple representations such as:

Bengaluru

Bangalore

BLR

bengaluru

Delhi

New Delhi

Gurgaon

MUM

HYD

JAI

These were normalized into six canonical city labels:

Bengaluru

Delhi NCR

Hyderabad

Pune

Mumbai

Jaipur

Test stations

Two test stations were identified using the STN-TST prefix and
separated from the clean station view used for network-performance
analysis.

Duplicate detection

The notebook flags likely near-duplicate swap events using:

Same rider

Same station

Events within 2 minutes

offline_batch synchronization mode

This identified 652 likely near-duplicate events.

Invalid distance values

Negative km_since_last_swap values were treated as invalid and
converted to missing values.

7,814 negative values were detected.

Percentage-value correction

Battery SOC/SOH-related percentage fields were checked for values above
100%.

The affected values were capped at 100 as part of the sensor-drift
correction.

Feature Engineering

The analysis creates several derived variables, including:

is_completed

event_date

hour_of_day

event_month

season

rider_tenure_days

energy_cost_inr

contribution_inr

soh_degradation

Rider activity and churn indicators

Station-level failure rates

Partner-level contribution metrics

The swap dataset is enriched using station, rider, battery, and partner
information.

Key Findings

1. Network Performance

Swap volume and revenue increase over the analyzed period.

However, the growth hides recurring operational problems:

Failure rates show recurring increases during April--June.

The notebook associates this recurring pattern with extreme-heat
periods.

A pricing change around July 2024 is associated with a higher
contribution per swap.

The seasonal failure pattern appears again in 2025.

The analysis therefore separates growth metrics from underlying
service-quality metrics rather than treating increasing revenue alone
as evidence of operational improvement.

2. Swap Failure Patterns

Failure rates vary substantially by charger generation.

Charger generation     Failure rate

Gen1                          7.23%
Gen2                          4.84%
Gen3                          4.84%

The notebook also identifies concentration during evening peak periods.

City-level failure rates in the analysis were:

City          Failure rate

Jaipur               7.85%
Delhi NCR            7.35%
Hyderabad            6.69%
Pune                 4.99%
Bengaluru            4.56%
Mumbai               4.45%

These are descriptive results from the analyzed dataset and should not
be interpreted as causal estimates.

3. Station & Geographic Patterns

The analysis compares performance across station location types and host
types.

Failure rates by location type were relatively close compared with the
differences observed across charger generations.

The notebook therefore treats location as useful context while
investigating equipment and operational characteristics as important
explanatory factors.

A major equipment finding is the difference in turnaround time:

Gen1: approximately 91.8 minutes

Gen3: approximately 42.1 minutes

The analysis also examines 3W station capacity and identifies stations
with high 3W demand relative to their inventory targets.

4. Battery & Equipment Performance

Battery supplier analysis shows substantial differences in degradation.

Supplier            Avg. degradation

Amptek       18.19 percentage points
Cellora      18.25 percentage points
Kyron        35.46 percentage points

The notebook also finds that lower battery SOH is associated with lower
observed distance between swaps:

SOH band     Avg. km since previous swap

<70                           44.23 km
70–80                         50.87 km
80–90                         56.92 km
90–100                        70.09 km

This analysis indicates a strong relationship between battery health and
observed range.

5. Pricing & Partner Economics

The analysis compares contribution across tariff types.

The peak tariff has a higher average contribution per completed swap
than the other analyzed tariff categories.

A major partner-level finding concerns ZipDrop:

Original discount: 12%

Post-amendment discount: 28%

Amendment date: November 1, 2024

Estimated contribution-margin reduction: ₹6.31 per swap

Estimated total margin impact in the analyzed post-amendment period:
approximately ₹21.5 lakh

The ₹21.5 lakh figure is an estimate produced by the notebook's
before/after comparison and should be interpreted in that context rather
than as a controlled causal estimate.

6. Rider Retention & Early Churn

The notebook defines an early-churn group as riders who:

Were active for 30 days or less from their first completed swap,
and

Had at least 60 days of subsequent data available to confirm
that they did not return.

Using this definition:

6,762 riders were observable for the churn analysis.

1,443 were classified as early churners.

Early churn rate: 21.34%

A key finding is the difference in personal failure experience:

Rider group     Avg. personal failure rate in first 30 days

Retained                                              5.37%
Early churn                                           7.93%

The notebook therefore identifies early service failure exposure as an
important factor associated with early churn.

The analysis also compares churn across:

Signup channel

City

Fleet affiliation

Vehicle class

Plan type

KYC verification

These comparisons are descriptive and do not establish that any one
variable independently causes churn.

Business Recommendations Derived from the Analysis

The notebook's final recommendation direction focuses on several
operational areas:

1. Prioritize Gen1 charger replacement

Gen1 chargers show higher failure rates and substantially longer
turnaround times in the analyzed data.

2. Prepare for seasonal heat-related failures

The recurring April--June failure pattern suggests that
thermal-management and summer-readiness measures should be investigated
before the next high-temperature period.

3. Review battery-supplier performance

The battery analysis identifies substantial differences in degradation
and observed range, particularly for Kyron in this dataset.

4. Review high-impact partner contracts

The ZipDrop contract amendment is associated with a significant
contribution-margin decline in the notebook's before/after analysis.

5. Monitor new-rider service quality

Because early churners experienced higher personal failure rates during
their first 30 days, reducing early failed swaps could be a useful
retention-focused operational metric.

Visualizations

The notebook generates visualizations for reporting, including:

Network performance trends

Failure-rate comparisons

Charger-generation analysis

Rider churn vs. service-failure experience

Other station, battery, pricing, and operational comparisons

Generated figures are saved under the project's outputs/figures/
directory when the corresponding notebook cells are executed.

Project Structure

A recommended GitHub structure is:

VoltRelay-Energy/
│
├── data/
│   └── raw/
│       ├── swap_events.csv
│       ├── station_hourly_status.csv
│       ├── riders.csv
│       ├── batteries.csv
│       ├── support_tickets.csv
│       ├── stations.csv
│       ├── city_daily_context.csv
│       └── fleet_partners.csv
│
├── notebooks/
│   └── VoltRelay_Energy_Network_Performance_Analysis.ipynb
│
├── outputs/
│   └── figures/
│
├── README.md
└── requirements.txt

Important

The raw data contains millions of rows. Before pushing the repository to
GitHub, check the dataset size and whether the hackathon permits public
redistribution of the provided data.

If the raw files are too large or are not permitted to be redistributed,
keep them locally and add them to .gitignore. The notebook can remain
in the repository with instructions explaining where the required files
should be placed.

Installation

Clone the repository:

git clone https://github.com/YOUR_USERNAME/VoltRelay-Energy.git
cd VoltRelay-Energy

Create a virtual environment:

Windows

python -m venv .venv
.venv\Scripts\activate

macOS / Linux

python3 -m venv .venv
source .venv/bin/activate

Install dependencies:

pip install -r requirements.txt

Start Jupyter:

jupyter notebook

Open the notebook under:

notebooks/

Requirements

Create a requirements.txt file containing:

numpy
pandas
matplotlib
seaborn
jupyter
notebook

Running the Analysis

Place the required CSV files inside:

data/raw/

Open the notebook.

Run the cells from top to bottom.

The notebook will:

Validate the input datasets

Inspect data types and missing values

Perform data-quality checks

Clean and normalize the data

Engineer analytical features

Join related datasets

Perform the six analytical investigations

Generate visualizations

Produce the final findings and recommendation direction

Reproducibility Notes

The notebook currently uses relative paths such as:

DATA_DIR = '../data/raw'

This means the notebook should be executed from the expected notebooks
directory structure.

If your notebook is stored elsewhere, update the path accordingly.

The notebook was developed with Python 3.13.7.

Limitations

This project is primarily a descriptive and diagnostic analytics study.

Important limitations include:

The analysis is based on the provided datasets.

Observed relationships do not automatically imply causation.

Some business-impact calculations are estimates based on
before/after comparisons.

The churn definition is an analytical definition created for this
project.

The dataset contains missing values and known data-quality issues
that require cleaning.

The notebook does not implement a production deployment or real-time
analytics pipeline.

Skills Demonstrated

This project demonstrates practical skills in:

Large-scale CSV data handling

Exploratory Data Analysis (EDA)

Data cleaning

Missing-value analysis

Data validation

Data normalization

Feature engineering

Multi-table data integration

GroupBy and aggregation

Time-series analysis

Operational analytics

Customer-retention analysis

Financial/business analytics

Data visualization

Translating data findings into business recommendations

Author

Puskar

GitHub: Puskar7882

Project Status

Completed for hackathon submission.