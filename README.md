### NYC Taxi Data Analytics — SQL, DuckDB & Dimensional Modeling Project
 Overview

This project builds a full end-to-end data analytics pipeline using the NYC Yellow Taxi dataset.
It replicates the type of workflow used in real data engineering & BI environments:

Acquire large transactional data

Clean and wrangle millions of records

Design a star-schema dimensional model

Load data into fact/dimension tables

Use SQL to answer business questions

Visualize findings and explain insights

The project simulates real-world analytics, not just queries, but modeling, validation, and interpretation.

  Dataset
Source: NYC Yellow Taxi Trip Dataset (Kaggle)


~11.9 million rides

Trip timestamps, locations, fare details, tips, and surcharges

Joined with official NYC Taxi Zone lookup for geographic context

Filtered to January–February 2020 (valid high-quality window)

🧹 Data Cleaning & Wrangling

The raw dataset was large and messy — mixed types, inconsistent years, missing timestamps, and numeric values stored as text.
A structured cleaning pipeline was used.

 1): Load & Inspect

Loaded to DuckDB (all_varchar=True to avoid ingestion failures)

Verified record count (~11.9M)

Inspected schema and sample rows using csvkit

 2): Fix Timestamps & Remove Invalid Years

The data contained stray entries (2003, 2008, 2009, 2019, 2021).

Actions taken:

Parsed timestamps with STRPTIME()

Grouped by year to identify anomalies

Kept only year 2020

Further restricted to January & February, because later months contained noise due to pandemic disruptions

Result: 11,890,396 valid records retained.

 3):  Handle Missing & Corrupt Records

Dropped rows missing pickup or drop-off timestamps

Ignored invalid timestamp conversions

Ensured every row represented a valid trip

 4): Convert Text → Proper Data Types

Because the CSV was imported as text:

Fares, tips, extras, surcharges → DECIMAL

Timestamps → TIMESTAMP

Zone IDs → INTEGER

 5): Enrich with Lookup Data

Joined the NYC Taxi Zone lookup table to convert numeric IDs into real places:

Borough

Zone

Service zone

This made geographic analysis meaningful.

 6): Create Clean Analytical Tables

Created filtered and enriched tables:

taxi_jan_feb_2020

taxi_jan_feb_merged

Row counts validated at every transformation step.

✅ Result of Cleaning

The pipeline produced:

✔️ reliable trip records
✔️ consistent timestamps
✔️ correct numeric values
✔️ business-ready enriched data

⭐ Dimensional Modeling (Star Schema)

Fact Table:

fact_trips

One row per trip

Measures: fare, tips, surcharges, total revenue, distance

Dimensions:

dim_datetime — year, month, hour, weekend flag

dim_location — zone, borough, service zone

dim_payment_type — payment categories

Role-playing dimensions are used for pickup vs drop-off.

Schema supports fast aggregation and flexible reporting.

🔍 Business Questions & Insights

1):  Which locations maximize demand & earnings?

Manhattan zones generate the highest trip volume

Airports (JFK, LaGuardia) produce fewer rides but higher fares

👉 Strategy: Core Manhattan = steady demand; Airports = high-value rides.

 2): How do payment types affect tipping?

Credit cards average ~15% tip

Cash appears as $0 because cash tips are not recorded electronically

👉 Insight: Digital payments increase visible/recorded tip revenue.

  3): What percentage of revenue is consumed by mandatory fees?

Mandatory fees (MTA tax + congestion + improvement surcharge) account for:

~16.6% of total revenue

👉 Nearly 1 out of every 6 passenger dollars goes to government fees.

 4): What time of day generates the highest revenue per trip?

Morning and evening rides deliver the strongest averages (commuter behavior).

📊 Visualizations

Generated using Matplotlib + Seaborn:

Top zones by demand

Tips by payment type

Revenue breakdown chart

Fee vs base fare contribution

Time-of-day revenue analysis

Figures saved and referenced in notebook.



Tools & Technologies
Area: 	Tools
ETL / Data Processing: DuckDB, csvkit, Python
Modeling: 	Dimensional modeling (Star schema)
Visualization: Matplotlib, Seaborn
Data Access: KaggleHub

