# 👩🏻‍⚕️ Medical Cost Personal Datasets

## 📌 Solution

## Quick Validation Process

### 1. Check the total number of rows:
```sql
SELECT COUNT(*) as total_rows
FROM insurance;
```
Answer:
<img width="106" height="43" alt="image" src="https://github.com/user-attachments/assets/8e017b27-1ef1-4f7f-a26a-c838701d56d1" />
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

