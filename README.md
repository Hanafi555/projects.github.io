# 📘 Case Studies and Assignments

Welcome to the repository where I track and link all completed assignments and case studies.

👉 🔗 Assignment 1 Files

- [Assignment 1.md](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%20_Casestudy/Assignment%201.md)
- [Casestudy 1.md](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%20_Casestudy/Casestudy_Videogame.md)


👉 🔗 Assignment 2 Files

Assignemnt 1:
- [assignment.md](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%20_Casestudy/Assignment%201.md)
- [case_study.md](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%20_Casestudy/Casestudy_Videogame.md)

---
Assignment 2:

- [Assignment 2.md](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%202_Casestudy/Assigment%202.md)
- [Casestudy 2.md](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment2_Casestudy/Casestudy2_COVID-19.md)

### 📊 Visual Outputs (Graphs & Images)
- [B_Norm Hist-1](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%202_Casestudy/image-1.png)
- [Bar Plot-2](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%202_Casestudy/image-2.png)
- [Box Plot-3](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%202_Casestudy/image-3.png)
- [Age Distribution & Death Rate](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%202_Casestudy/image-4.png)
- [Death Rate Among COVID-Positive](https://github.com/Hanafi555/projects.github.io/blob/main/Assignment%202_Casestudy/image-5.png)

---SSSSSSSSSSS
Project on Citibike dataset (US)
## 🔗 Citibike Full Case Study 

👉 Visit the full repo: [Citibike Project](https://github.com/apache088/ntu-sctp-dsai1f-project-team6/tree/hanafi_branch)

Citibike Trip Data Transformation
Feature Engineering
Synopsis: This section explains how new features were created or selected to improve model performance.

S/N	Table	New Feature(s)	Implementation Logic	Key Benefit(s)
1	fact_trip	duration_mins	Computing difference between start and end times	Key metric used for analysis; no additional computation is required during analysis
2	fact_trip	distance_m	Using BigQuery’s functions ( Haversine formula)	Can be used to estimate the trip distance in metres
3	fact_trip	price_paid	Referencing price_plans seed table to compute price paid per trip	Key metric to determine revenue impact by stations; no additional computation is required during analysis
4	dim_stations	Every station as an unique record	Using CTE to combine all start and end stations into a temp table and group them by four columns to uniquely identify every station	Normalised data facilitates analysis at per station level
5	dim_stations	total_starts, total_ends	Using a temp column to store roles of every station and counting how many times each station is used as the start station for trips, likewise for end station	Useful indicators to quickly identify most used or least used stations
Model Development
Synopsis: This section documents the models built, algorithms used, and rationale for their selection.

There are four models built (excluding the price_plans seed table which contains static/reference data): fact_trips, dim_stations, dim_membership_types, dim_bike_types.

All models are built based on the source dataset, with new features added as per described in the preceding section. Full implementation logic is shown in the following code snippets.

As the source dataset does have incomplete data, several implementation techniques are applied to mitigate errors during execution of the data pipeline.

Defensive coding (applicable to all tables)

Explicit casting to mitigate inference differences between dbt and BigQuery
Using COALESCE function to mitigate errors due to missing/null values
Using common environment variables by implementing python-dotenv


Citibike Trip Data ELT Orchestration
Overview
This is a Dagster project that automates the running of:

Meltano ingestion
Extraction from DuckDB database.
Uploading data to Google BigQuery as a table.
Implementation:
Uses a Python subprocess to run a meltano command.
dbt transformation
Performs all the data transformation steps configured in dbt project.
Implementation:
Uses dbt CLI to run dbt build.

