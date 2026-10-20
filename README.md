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

### 4. Are categorical values consistent, or are numeric values within plausible ranges?
```sql
SELECT DISTINCT sex, region, smoker
FROM insurance;

SELECT
    MIN(age) AS min_age,
    MAX(age) AS max_age,
    MIN(bmi) AS min_bmi,
    MAX(bmi) AS max_bmi,
    MIN(children) AS min_children,
    MAX(children) AS max_children,
    MIN(charges) AS min_charges,
    MAX(charges) AS max_charges
FROM insurance;
```
Answer: 
<img width="168" height="46" alt="Screenshot 2026-10-20 111423" src="https://github.com/user-attachments/assets/7022766d-8652-43d5-a82d-1d8d93af6152" />
<img width="593" height="34" alt="Screenshot 2026-10-20 111619" src="https://github.com/user-attachments/assets/7236102e-e9fc-47fd-8d40-4b6728ec0c07" />

Note: BMI of 53.31 may need further validation.





