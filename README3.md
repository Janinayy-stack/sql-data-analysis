# 🧹 SQL Data Cleaning Practice - USES SQLlite

## 📌 Solution

## Prepare the data
```sql

CREATE TABLE facility_testing (
    facility VARCHAR(50),
    region VARCHAR(50),
    tests INT,
    positive INT,
    status VARCHAR(20)
);

INSERT INTO facility_testing VALUES
('Facility A', 'NCR', 120, 4, 'Complete'),
(' Facility B', 'ncr', 250, NULL, 'COMPLETE'),
('facility C', 'Region III', 180, 6, 'Pending'),
('Facility D', 'REGION III', NULL, 10, 'Complete'),
('Facility E ', 'Region IV-A', 300, 15, 'complete'),
('Facility E', 'Region IV-A', 300, 15, 'complete');
```
## Inspect data
```sql
-- Inspect data --
SELECT *
FROM facility_testing;

PRAGMA table_info(facility_testing);
-- Count rows
SELECT COUNT(*)
FROM facility_testing;

-- Check unique values
SELECT DISTINCT region
FROM facility_testing;

SELECT DISTINCT status
FROM facility_testing;

SELECT DISTINCT facility
FROM facility_testing;

-- Check missing data
SELECT
    SUM(CASE WHEN facility IS NULL THEN 1 ELSE 0 END)
        AS missing_facility,
    SUM(CASE WHEN region IS NULL THEN 1 ELSE 0 END)
        AS missing_region,
    SUM(CASE WHEN tests IS NULL THEN 1 ELSE 0 END)
        AS missing_tests,
    SUM(CASE WHEN positive IS NULL THEN 1 ELSE 0 END)
        AS missing_positive,
    SUM(CASE WHEN status IS NULL THEN 1 ELSE 0 END)
        AS missing_status
FROM facility_testing;

-- Display all rows containing missing values
SELECT *
FROM facility_testing
WHERE facility IS NULL
   OR region IS NULL
   OR tests IS NULL
   OR positive IS NULL
   OR status IS NULL;
```
Answer: <img width="529" height="86" alt="image" src="https://github.com/user-attachments/assets/2f988bc3-6ad5-41af-9006-98a9fce72685" />

## Cleaning

```sql
-- Preview text cleaning
SELECT
    UPPER(TRIM(facility)) AS facility,
    UPPER(TRIM(region)) AS region,
    LOWER(TRIM(status)) AS status
FROM facility_testing;

-- Update and save text cleaning
UPDATE facility_testing
SET
    facility = UPPER(TRIM(facility)),
    region = UPPER(TRIM(region)),
    status = LOWER(TRIM(status));

-- Verify the saved changes
SELECT *
FROM facility_testing;

-- Verify distinct  values
SELECT DISTINCT facility
FROM facility_testing;

SELECT DISTINCT region
FROM facility_testing;

SELECT DISTINCT status
FROM facility_testing;
```
Answer: <img width="528" height="186" alt="image" src="https://github.com/user-attachments/assets/021f371d-3faa-4906-9f0f-59b2b3e00f81" />

## Find duplicates
```sql
SELECT
    facility,
    region,
    tests,
    positive,
    status,
    COUNT(*) AS duplicate_count
FROM facility_testing
GROUP BY
    facility,
    region,
    tests,
    positive,
    status
HAVING COUNT(*) > 1;
```
Answer: <img width="473" height="74" alt="image" src="https://github.com/user-attachments/assets/eb07f542-3b45-4461-9c79-788276cfb778" />

## Remove duplicates
```sql
DELETE FROM facility_testing
WHERE rowid IN (
    SELECT source_row
    FROM (
        SELECT
            rowid AS source_row,
            ROW_NUMBER() OVER (
                PARTITION BY
                    facility,
                    region,
                    tests,
                    positive,
                    status
                ORDER BY rowid
            ) AS duplicate_rank
        FROM facility_testing
    ) AS ranked_rows
    WHERE duplicate_rank > 1
);

-- Verify remaining duplicates
SELECT
    facility,
    region,
    tests,
    positive,
    status,
    COUNT(*) AS duplicate_count
FROM facility_testing
GROUP BY
    facility,
    region,
    tests,
    positive,
    status
HAVING COUNT(*) > 1;
```
Answer: <img width="353" height="41" alt="image" src="https://github.com/user-attachments/assets/9616c923-f8b9-4c53-b2f6-642db55d1836" />

## Handling NULL values
- COALESCE substitutes zero in this query's output.
```sql
SELECT
    facility,
    positive,
    COALESCE(positive, 0) AS positive_if_assumed_zero
FROM facility_testing;
```
Answer: <img width="343" height="164" alt="image" src="https://github.com/user-attachments/assets/fba600ab-37c1-466b-bb98-11fb6084f6cf" />

## Add calculations

```sql
ALTER TABLE facility_testing
ADD COLUMN negative INTEGER;

ALTER TABLE facility_testing
ADD COLUMN positivity_rate REAL;

-- Negative cases and positivity rate
UPDATE facility_testing
SET
    negative = CASE
        WHEN tests IS NOT NULL
         AND positive IS NOT NULL
        THEN tests - positive
        ELSE NULL
    END,
    positivity_rate = ROUND(
        positive * 100.0 / NULLIF(tests, 0),
        2
    );

-- CREATE A HIGH-POSITIVE FLAG

-- 1 = more than 10 positive cases
-- 0 = 10 or fewer positive cases
-- NULL = positive count is unknown

SELECT
    facility,
    positive,
    CASE
        WHEN positive IS NULL THEN NULL
        WHEN positive > 10 THEN 1
        ELSE 0
    END AS high_positive_flag
FROM facility_testing;
```
Answer: <img width="302" height="179" alt="image" src="https://github.com/user-attachments/assets/5c8c971e-eb55-4482-965b-a7b68ad17aaf" />

## Validate Data Quality
```sql
SELECT *
FROM facility_testing
WHERE tests IS NOT NULL
  AND positive IS NOT NULL
  AND positive > tests;

-- Check for negative input counts.

SELECT *
FROM facility_testing
WHERE tests < 0
   OR positive < 0;

-- Check remaining missing values.

SELECT *
FROM facility_testing
WHERE facility IS NULL
   OR region IS NULL
   OR tests IS NULL
   OR positive IS NULL
   OR status IS NULL;

```
Answer: <img width="530" height="111" alt="image" src="https://github.com/user-attachments/assets/0a546d90-6169-43e3-a33c-84c65b3e6e80" />

## View Final dataset
```sql
SELECT
    facility,
    region,
    tests,
    positive,
    negative,
    positivity_rate,
    CASE
        WHEN positive IS NULL THEN NULL
        WHEN positive > 10 THEN 1
        ELSE 0
    END AS high_positive_flag,
    status
FROM facility_testing
ORDER BY region, facility;
```
Answer: <img width="655" height="186" alt="image" src="https://github.com/user-attachments/assets/64143a29-4bc1-46a8-b14d-5989f0462717" />

## Final validation

```sql
-- Final row count
SELECT COUNT(*) AS final_row_count
FROM facility_testing;

-- Count duplicate groups remaining
SELECT COUNT(*) AS duplicate_groups_remaining
FROM (
    SELECT
        facility,
        region,
        tests,
        positive,
        status,
        COUNT(*) AS duplicate_count
    FROM facility_testing
    GROUP BY
        facility,
        region,
        tests,
        positive,
        status
    HAVING COUNT(*) > 1
);
```
Answer: <img width="233" height="79" alt="image" src="https://github.com/user-attachments/assets/10d277db-b3b0-4377-9130-4f87337fc748" />






