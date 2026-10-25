# 👩🏻‍⚕️ PUBLIC HEALTH FACILITY SQL PROJECT PART I - USES MYSQL/SQLITE

## 📌 Solution

## 1. Create table
```sql
CREATE TABLE facilities (
    Facility TEXT,
    Region TEXT,
    Tests INTEGER,
    Positive INTEGER,
    Reporting_Status TEXT
);
```
## 2. Insert data
```sql
INSERT INTO facilities (Facility, Region, Tests, Positive, Reporting_Status)
VALUES
('Facility A', 'NCR', 120, 15, 'Complete'),
('Facility B', 'Region III', 200, 30, 'Complete'),
('Facility C', 'NCR', 150, 18, 'Pending'),
('Facility D', 'Region IV-A', 250, 40, 'Complete'),
('Facility E', 'Region III', 180, 12, 'Pending');
```

## 3. Show all records
```sql
SELECT *
FROM facilities;
```
Answer: <img width="380" height="105" alt="image" src="https://github.com/user-attachments/assets/78a1e5d9-256a-45e5-a7f2-2123c9943942" />

## 4. Facilities with 20 or more positive cases
```sql
SELECT Facility, Positive
FROM facilities
WHERE Positive >= 20;
```
Answer: <img width="149" height="59" alt="image" src="https://github.com/user-attachments/assets/3f653ff7-f513-4ccd-8514-c84f5e606ea5" />

## 5. Facilities with more than 150 tests
```sql
SELECT Facility, Tests
FROM facilities
WHERE Tests > 150;
```
Answer: <img width="140" height="81" alt="image" src="https://github.com/user-attachments/assets/33cabc46-8d42-4e5e-a401-54ea70c5c436" />

## 6.  Facilities in Region III
```sql
SELECT *
FROM facilities
WHERE Region = 'Region III';
```
Answer: <img width="370" height="60" alt="image" src="https://github.com/user-attachments/assets/c6e705b3-ba11-4917-a729-3f43a58b67b2" />

## 7. Facilities with 150 or fewer tests
```sql
SELECT Facility, Tests
FROM facilities
WHERE Tests <= 150;
```
Answer: <img width="149" height="72" alt="image" src="https://github.com/user-attachments/assets/8713c4d6-1169-4068-9b1f-0f5b1d64a1be" />

## 8. Facilities where Positive is not 15
```sql
SELECT *
FROM facilities
WHERE Positive <> 15;
```
Answer: <img width="368" height="96" alt="image" src="https://github.com/user-attachments/assets/e2dba2b5-661c-4f0d-915e-2bf2d783cca5" />

## 9. Facilities in NCR
```sql
SELECT Facility, Positive
FROM facilities
WHERE Region = 'NCR';
```
Answer: <img width="144" height="71" alt="image" src="https://github.com/user-attachments/assets/9a7d62de-e0c7-4faa-a324-334f8a3a831e" />

# 👩🏻‍⚕️ PUBLIC HEALTH FACILITY SQL PROJECT PART II

## 📌 Solution

## 1. Create table
```sql
CREATE TABLE facilities1 (
	facility_id TEXT, 
    facility_name TEXT, 
    region TEXT
);

CREATE TABLE monthly_monitoring (
	facility_id TEXT, 
    month DATE,
    clients_tested INTEGER,
    positive_cases INTEGER,
    linked_to_care INTEGER,
    vl_tested INTEGER,
    vl_suppressed INTEGER
);
```

## Part A — Basic Analysis - USES SQLITE

## Question 1 - Using a JOIN, calculate the total clients tested by facility.

```sql
SELECT facility_name, SUM(clients_tested)
FROM monthly_monitoring as m
INNER JOIN facilities as f
USING (facility_id)
GROUP BY facility_name
ORDER BY clients_tested DESC;

```
Answer:
<img width="324" height="184" alt="image" src="https://github.com/user-attachments/assets/2d4d34ad-238f-4f5e-80fb-f6b3f9c8beb8" />

## Question 2 - Calculate the positivity rate for each facility.
```sql
SELECT
    facility_name,
    region,
    SUM(clients_tested) AS total_clients_tested,
    SUM(positive_cases) AS total_positive_cases,
    ROUND(
    SUM(positive_cases) * 100.0 / SUM(clients_tested),
    2
) AS positivity_rate
FROM monthly_monitoring AS m
INNER JOIN facilities AS f
USING (facility_id)
GROUP BY facility_name, region;
```
Answer: <img width="602" height="178" alt="image" src="https://github.com/user-attachments/assets/78e03132-b2ae-459d-8159-f065a9537c6e" />

## Question 3 -- classify each facility based on clients_tested
```sql
SELECT
    facility_name,
    region,
    SUM(clients_tested) AS total_clients_tested,
    CASE
        WHEN SUM(clients_tested) >= 1600 THEN 'High Volume'
        WHEN SUM(clients_tested) >= 1200 THEN 'Medium Volume'
        ELSE 'Low Volume'
    END AS test_classification
FROM monthly_monitoring AS m
INNER JOIN facilities AS f
USING (facility_id)
GROUP BY facility_name, region;
```
Answer: <img width="494" height="179" alt="image" src="https://github.com/user-attachments/assets/a12e3f21-d986-4f55-8081-9e4e64841083" />

## Question 4 - Subqueries -- Facilities above the overall facility average
```sql
SELECT
    f.facility_name,
    SUM(m.clients_tested) AS total_clients_tested, 
	AVG(clients_tested) AS avg_clients_tested
FROM monthly_monitoring AS m
INNER JOIN facilities AS f
USING (facility_id)
GROUP BY f.facility_name
HAVING SUM(m.clients_tested) > (
    SELECT AVG(total_clients_tested)
    FROM (
        SELECT
            SUM(clients_tested) AS total_clients_tested
        FROM monthly_monitoring
        GROUP BY facility_id
    )
);
```
Answer: <img width="440" height="99" alt="image" src="https://github.com/user-attachments/assets/e855ed37-d482-4b5d-8cbc-b6c53d151371" />

## Question 5 - Identify facilities - Positivity Rate above the overall facility average
```sql
SELECT
	facility_id,
	SUM(clients_tested) AS total_clients_tested,
    SUM(positive_cases) AS total_positive_cases,
	ROUND(SUM(positive_cases) * 100.0 / SUM(clients_tested),
    2
) AS positivity_rate
FROM monthly_monitoring AS m
INNER JOIN facilities AS f
USING (facility_id)
GROUP BY m.facility_id
HAVING positivity_rate > (
    SELECT
        SUM(positive_cases) * 100.0 / SUM(clients_tested)
    FROM monthly_monitoring
);
```
Answer: <img width="428" height="84" alt="image" src="https://github.com/user-attachments/assets/446d8db6-ab3d-43d1-a3b2-1fe0fa4b9095" />

## Question 6 - Create a CTE that calculates facility-level totals:
```sql
WITH match_list AS (
    SELECT 
        facility_id,
        SUM(clients_tested) AS total_tested, 
        SUM(positive_cases) AS total_positive,
        SUM(linked_to_care) AS total_linked_care,
        ROUND(
            SUM(linked_to_care) * 100.00 / SUM(positive_cases),
            2
        ) AS linkage_rate
    FROM monthly_monitoring AS m
    GROUP BY facility_id
)
SELECT
    f.facility_name,
    match_list.total_tested,
    match_list.total_positive,
    match_list.total_linked_care,
    match_list.linkage_rate
FROM facilities AS f
LEFT JOIN match_list
    ON f.facility_id = match_list.facility_id
ORDER BY match_list.linkage_rate DESC;
```
Answer: <img width="543" height="176" alt="image" src="https://github.com/user-attachments/assets/a1abece0-8813-4bad-9cb5-166ed12ca969" />

## Question 7 - Using a CTE, calculate the monthly positivity rate by region.
```sql
WITH match_list AS (
    SELECT 
        f.region,
        m.month,
        SUM(m.clients_tested) AS total_tested, 
        SUM(m.positive_cases) AS total_positive,
        ROUND(
            SUM(m.positive_cases) * 100.0 / SUM(m.clients_tested),
            2
        ) AS positivity_rate
    FROM monthly_monitoring AS m
    INNER JOIN facilities AS f
        ON m.facility_id = f.facility_id
    GROUP BY f.region, m.month
)
SELECT 
    region,
    month,
    total_tested,
    total_positive,
    positivity_rate
FROM match_list
ORDER BY month, region;
```
Answer: <img width="444" height="337" alt="image" src="https://github.com/user-attachments/assets/b554b314-8b9d-4677-bdf1-a5d5b39d4aed" />

## Question 8 - Window Functions: Rank facilities each region based on total positive cases.
```sql
SELECT 
	f.region,
	SUM(positive_cases) AS total_positive,
	RANK() OVER(ORDER BY SUM(positive_cases)) AS rank_positive
FROM monthly_monitoring AS m
LEFT JOIN facilities as f
USING(facility_id)
GROUP BY region
ORDER BY rank_positive;

-- Filler: Window Functions: Rank facilities WITHIN each region based on total positive cases.
SELECT 
    f.facility_name,
    f.region,
    SUM(m.positive_cases) AS total_positive,
    RANK() OVER (
        PARTITION BY f.region
        ORDER BY SUM(m.positive_cases) DESC
    ) AS rank_positive
FROM monthly_monitoring AS m
LEFT JOIN facilities AS f
USING (facility_id)
GROUP BY f.facility_name, f.region
ORDER BY f.region, rank_positive;
```
Answer: <img width="446" height="188" alt="image" src="https://github.com/user-attachments/assets/6f6ad466-12fb-4506-809c-8c3d27e83847" />

## Question 9 - Month-to-Month Change - For each facility, calculate the previous month's number of clients tested.
```sql
SELECT
    facility_id,
    month,
    clients_tested,
    LAG(clients_tested) OVER (
        PARTITION BY facility_id
        ORDER BY month
    ) AS previous_month_tested
FROM monthly_monitoring;
```
Answer: <img width="624" height="655" alt="image" src="https://github.com/user-attachments/assets/d8622241-5fdd-4fdb-9e0d-d8b275b3f715" />

## Question 10 — Running Total -- Calculate the cumulative number of positive cases for each facility over time.
```sql
SELECT 
    f.facility_name,
    f.region,
    m.month,
    m.positive_cases,
    SUM(m.positive_cases) OVER (
        PARTITION BY m.facility_id
        ORDER BY m.month
    ) AS cumulative_positive_cases
FROM monthly_monitoring AS m
LEFT JOIN facilities AS f
USING (facility_id)
ORDER BY f.facility_name, m.month;
```
Answer: <img width="420" height="651" alt="image" src="https://github.com/user-attachments/assets/d4829d24-6d1e-4793-ab01-7f0b68126095" />

## Question 11 - Program Monitoring Challenge. Identify the highest-performing facility in each region based on linkage rate.

1. Calculate total positive cases and total linked-to-care cases
2. Calculate linkage rate.
3. Rank facilities within each region.
4.  Return only the facility ranked #1 in each region.

```sql
WITH facility_summary AS (
    SELECT
        f.facility_name,
        f.region,
        SUM(m.positive_cases) AS total_positive,
        SUM(m.linked_to_care) AS total_linked,
        ROUND(
            SUM(m.linked_to_care) * 100.0 /
            SUM(m.positive_cases),
            2
        ) AS linkage_rate
    FROM monthly_monitoring AS m
    INNER JOIN facilities AS f
        USING (facility_id)
    GROUP BY f.facility_name, f.region
),

ranked_facilities AS (
    SELECT
        *,
        RANK() OVER (
            PARTITION BY region
            ORDER BY linkage_rate DESC
        ) AS regional_rank
    FROM facility_summary
)

SELECT *
FROM ranked_facilities
WHERE regional_rank = 1;
```
Answer: <img width="594" height="112" alt="image" src="https://github.com/user-attachments/assets/18e706cb-aae5-4c05-9cd6-7f08575610e7" />






