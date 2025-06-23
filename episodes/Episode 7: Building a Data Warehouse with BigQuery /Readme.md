
# Episode 7: Building a Data Warehouse with BigQuery

In this episode of the "8 Reasons Why GCP" series, we focus on designing and populating a data warehouse using Google BigQuery. This was one of the most dynamic labs—each refresh generated a new setup, making the experience refreshingly different and intellectually rewarding.

## 🧠 Key Concepts Covered
- Creating partitioned and expiration-managed tables
- Filtering and loading COVID-19 policy tracker data from public datasets
- Working with nested STRUCT fields to store multi-field records
- Enriching datasets via joins with ECDC and Census Bureau datasets
- Querying for missing data and debugging inconsistent fields

## 🏗️ Steps Performed

### 1. Create a Dataset and Partitioned Table
```sql
CREATE SCHEMA covid OPTIONS(location="US");

CREATE TABLE covid.oxford_policy_tracker
PARTITION BY date
OPTIONS(partition_expiration_days=1445)
AS
SELECT * 
FROM `bigquery-public-data.covid19_govt_response.oxford_policy_tracker`
WHERE alpha_3_code NOT IN ('GBR', 'BRA', 'CAN', 'USA');
```

### 2. Add Columns to Existing Table
```sql
ALTER TABLE covid_data.global_mobility_tracker_data
ADD COLUMN population INT64;

ALTER TABLE covid_data.global_mobility_tracker_data
ADD COLUMN country_area FLOAT64;

ALTER TABLE covid_data.global_mobility_tracker_data
ADD COLUMN mobility STRUCT<
  avg_retail FLOAT64,
  avg_grocery FLOAT64,
  avg_parks FLOAT64,
  avg_transit FLOAT64,
  avg_workplace FLOAT64,
  avg_residential FLOAT64>;
```

### 3. Update Population Field
```sql
UPDATE covid_data.consolidate_covid_tracker_data t0
SET t0.population = t2.pop_data_2019
FROM (
  SELECT DISTINCT country_territory_code, pop_data_2019
  FROM `bigquery-public-data.covid19_ecdc.covid_19_geographic_distribution_worldwide`
) AS t2
WHERE t0.alpha_3_code = t2.country_territory_code;
```

### 4. Update Area Field
```sql
UPDATE covid_data.consolidate_covid_tracker_data t0
SET t0.country_area = t2.country_area
FROM (
  SELECT country_name, country_area
  FROM `bigquery-public-data.census_bureau_international.country_names_area`
) AS t2
WHERE LOWER(t0.country_name) = LOWER(t2.country_name);
```

### 5. Populate Mobility STRUCT Field
```sql
UPDATE covid_data.consolidate_covid_tracker_data AS t1
SET mobility = STRUCT(
  t2.avg_retail,
  t2.avg_grocery,
  t2.avg_parks,
  t2.avg_transit,
  t2.avg_workplace,
  t2.avg_residential
)
FROM (
  SELECT
    country_region,
    date,
    AVG(retail_and_recreation_percent_change_from_baseline) AS avg_retail,
    AVG(grocery_and_pharmacy_percent_change_from_baseline) AS avg_grocery,
    AVG(parks_percent_change_from_baseline) AS avg_parks,
    AVG(transit_stations_percent_change_from_baseline) AS avg_transit,
    AVG(workplaces_percent_change_from_baseline) AS avg_workplace,
    AVG(residential_percent_change_from_baseline) AS avg_residential
  FROM `bigquery-public-data.covid19_google_mobility.mobility_report`
  GROUP BY country_region, date
) AS t2
WHERE LOWER(t1.country_name) = LOWER(t2.country_region)
AND t1.date = t2.date;
```

## 📌 Key Takeaway
Working with dynamic, schema-rich datasets under shifting constraints prepares you for real-world cloud analytics and ML feature engineering.
