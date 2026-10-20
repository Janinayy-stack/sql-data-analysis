# 👩🏻‍⚕️ Medical Cost Personal Datasets

## 📌 Solution

## Analysis 1: Data quality assessment

### 1. Check the total number of rows:
```sql
SELECT COUNT(*) as total_rows
FROM insurance;
```
Answer:
<img width="93" height="41" alt="image" src="https://github.com/user-attachments/assets/472e9d82-dfb4-4bfd-b815-249a9912a991" />

### 2. Check for missing values:
```sql
SELECT
    SUM(age IS NULL) AS missing_age,
    SUM(sex IS NULL) AS missing_sex,
    SUM(bmi IS NULL) AS missing_bmi,
    SUM(children IS NULL) AS missing_children,
    SUM(smoker IS NULL) AS missing_smoker,
    SUM(region IS NULL) AS missing_region,
    SUM(charges IS NULL) AS missing_charges
FROM insurance;
```
Answer:
<img width="605" height="33" alt="image" src="https://github.com/user-attachments/assets/d5a73272-122b-45ba-9cdc-b9484e0e3379" />  
There are no missing values in any of the variables.

### 3. Check for duplicates based on row:
```sql
SELECT
    age,
    sex,
    bmi,
    children,
    smoker,
    region,
    charges,
    COUNT(*) AS row_count
FROM insurance
GROUP BY
    age,
    sex,
    bmi,
    children,
    smoker,
    region,
    charges
HAVING COUNT(*) > 1;
```
Answer:
<img width="450" height="47" alt="Screenshot 2026-10-20 110449" src="https://github.com/user-attachments/assets/9ffa549e-8126-4e63-8237-6259a0381756" />



