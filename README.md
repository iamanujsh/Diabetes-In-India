# Diabetes Data Analysis

This project focuses on analyzing a diabetes dataset to uncover key insights and trends using SQL. The dataset contains various features like age, BMI, insulin levels, and glucose concentration, helping identify patterns in diabetes occurrence, risk factors, and patient demographics. By applying SQL queries, we can derive useful insights to understand how different factors contribute to diabetes and the effectiveness of treatments.

## CREATE DATABASE and Table Selection
```sql
CREATE DATABASE Diabetes

USE Diabetes

SELECT * FROM [dbo].[diabetes_young_adults_india]
```

## 1. What is the average BMI for each region?
```sql
SELECT Region, CAST(AVG(BMI) AS NUMERIC(10,2)) AS [Average BMI]
FROM diabetes_young_adults_india
GROUP BY Region
```

## 2. How many people have Type 1 diabetes vs. Type 2 diabetes?
```sql
SELECT Diabetes_Type, COUNT(DISTINCT id) AS [Total Patient]
FROM diabetes_young_adults_india
WHERE NOT Diabetes_Type = 'None'
GROUP BY Diabetes_Type
```

## 3. What are the top 5 individuals with the highest cholesterol levels?
```sql
SELECT TOP 5 *
FROM diabetes_young_adults_india
ORDER BY Cholesterol_Level DESC
```

## 4. What is the average HbA1c for people with prediabetes?
```sql
SELECT AVG(HbA1c) AS [AVG_HbA1c]
FROM diabetes_young_adults_india
WHERE Prediabetes = 1
```

## 5. How many people have a sedentary lifestyle vs. active lifestyle, and how many of them smoke or don’t smoke?
```sql
SELECT Physical_Activity_Level,
       COUNT(Physical_Activity_Level) AS [Total People],
       COUNT(CASE WHEN Smoking = 1 THEN 1 END) AS [Total People Who Smoke],
       COUNT(CASE WHEN Smoking = 0 THEN 1 END) AS [Total People Who Don’t Smoke]
FROM diabetes_young_adults_india
WHERE NOT Physical_Activity_Level = 'Moderate'
GROUP BY Physical_Activity_Level
```

## 6. What is the average stress level for each region?
```sql
SELECT Region, AVG(Stress_Level) AS [Average Stress Level]
FROM diabetes_young_adults_india
GROUP BY Region
```

## 7. Which individuals are sedentary and have unhealthy dietary habits?
```sql
SELECT *
FROM diabetes_young_adults_india
WHERE Physical_Activity_Level = 'Sedentary' AND Dietary_Habits = 'Unhealthy'
```

## 8. What is the average family income for individuals with Type 2 diabetes?
```sql
SELECT Diabetes_Type, CAST(AVG(CAST(Family_Income AS DECIMAL(18,2))) AS NUMERIC(18,2)) AS [Average Family Income]
FROM diabetes_young_adults_india
WHERE Diabetes_Type = 'Type 2'
GROUP BY Diabetes_Type
```

## 9. What percentage of people in each region have prediabetes?
```sql
SELECT Region,
       CAST((COUNT(CASE WHEN Prediabetes = 1 THEN 1 END) * 100.0 / COUNT(*)) AS NUMERIC(10, 2)) AS [% of Prediabetes / Region]
FROM diabetes_young_adults_india
GROUP BY Region;
```

## 10. Who are the individuals under 18 with Type 1 diabetes?
```sql
SELECT *
FROM diabetes_young_adults_india
WHERE Age < 18 AND Diabetes_Type = 'Type 1'
```

## 11. What is the average fasting blood sugar for people with Type 2 diabetes?
```sql
SELECT Diabetes_Type, CAST(AVG(Fasting_Blood_Sugar) AS NUMERIC(10,2)) AS [AVG Fasting Blood Sugar]
FROM diabetes_young_adults_india
GROUP BY Diabetes_Type
HAVING Diabetes_Type = 'Type 2'
```

## 12. What is the relationship between screen time and stress levels?
### Approach 1:
```sql
SELECT 
    CASE
        WHEN Screen_Time BETWEEN 1 AND 4 THEN '1-4 Hour'
        WHEN Screen_Time BETWEEN 4 AND 8 THEN '4-8 Hour'
        WHEN Screen_Time BETWEEN 8 AND 12 THEN '8-12 Hour'
    END AS [Screen Time],
    AVG(Stress_Level) AS [AVG Stress Level]
FROM diabetes_young_adults_india
GROUP BY 
    CASE
        WHEN Screen_Time BETWEEN 1 AND 4 THEN '1-4 Hour'
        WHEN Screen_Time BETWEEN 4 AND 8 THEN '4-8 Hour'
        WHEN Screen_Time BETWEEN 8 AND 12 THEN '8-12 Hour'
    END
```

### Approach 2:
```sql
SELECT 
    CASE
        WHEN Stress_Level BETWEEN 1 AND 3 THEN 'Low Stress'
        WHEN Stress_Level BETWEEN 3 AND 6 THEN 'Moderate Stress'
        WHEN Stress_Level > 6 THEN 'High Stress'
    END AS [Stress],
    CAST(AVG(Screen_Time) AS NUMERIC(10,3)) AS [Average Screen Time]
FROM diabetes_young_adults_india
GROUP BY 
    CASE
        WHEN Stress_Level BETWEEN 1 AND 3 THEN 'Low Stress'
        WHEN Stress_Level BETWEEN 3 AND 6 THEN 'Moderate Stress'
        WHEN Stress_Level > 6 THEN 'High Stress'
    END
```

## 13. What are the common physical activity levels among individuals with a high genetic risk score?
```sql
SELECT 
    CASE 
        WHEN Genetic_Risk_Score BETWEEN 1 AND 3 THEN 'Low Genetic Risk Score'
        WHEN Genetic_Risk_Score BETWEEN 3 AND 6 THEN 'Moderate Genetic Risk Score'
        WHEN Genetic_Risk_Score > 6 THEN 'High Genetic Risk Score'
    END AS Genetic_Risk_Category,
    Physical_Activity_Level,
    COUNT(*) AS Activity_Count
FROM diabetes_young_adults_india
GROUP BY
    CASE 
        WHEN Genetic_Risk_Score BETWEEN 1 AND 3 THEN 'Low Genetic Risk Score'
        WHEN Genetic_Risk_Score BETWEEN 3 AND 6 THEN 'Moderate Genetic Risk Score'
        WHEN Genetic_Risk_Score > 6 THEN 'High Genetic Risk Score'
    END,
    Physical_Activity_Level
ORDER BY Genetic_Risk_Category, Activity_Count DESC
```

## 14. How many individuals with a family history of diabetes have diabetes themselves?
```sql
SELECT COUNT(*) AS [Total Diabetes Count Based On Family History]
FROM diabetes_young_adults_india
WHERE Family_History_Diabetes = 1 AND Diabetes_Type IN ('Type 1', 'Type 2')
```

## 15. What is the average family income for people with different levels of physical activity?
```sql
SELECT Physical_Activity_Level, CAST(AVG(CAST(Family_Income AS DECIMAL(10,2))) AS NUMERIC(10,2)) AS [Avg Family Income]
FROM diabetes_young_adults_india
GROUP BY Physical_Activity_Level
```

## 16. How many individuals have a high cholesterol level (e.g., above 240)?
```sql
SELECT COUNT(*) AS [Total Individual]
FROM diabetes_young_adults_india
WHERE Cholesterol_Level > 240
```

## 17. What is the average age of individuals who consume fast food regularly?
```sql
SELECT AVG(Age) AS [Average Age]
FROM diabetes_young_adults_india
WHERE Dietary_Habits = 'Unhealthy' AND Fast_Food_Intake > 4
```

## 18. How does alcohol consumption impact the stress levels of individuals with diabetes?
```sql
WITH AverageStress AS (
    SELECT AVG(Stress_Level) AS [ASL_Consume_Alcohol]
    FROM diabetes_young_adults_india
    WHERE Diabetes_Type IN ('Type 1', 'Type 2') AND Alcohol_Consumption = 1
)
SELECT [ASL_Consume_Alcohol], 
       (SELECT AVG(Stress_Level) 
        FROM diabetes_young_adults_india
        WHERE Diabetes_Type IN ('Type 1', 'Type 2') AND Alcohol_Consumption = 0) AS [ASL_Not_Consume_Alcohol]
FROM AverageStress
```

## 19. What is the distribution of BMI levels for individuals with Type 1 vs. Type 2 diabetes?
```sql
SELECT Diabetes_Type, 
       CAST(AVG(BMI) AS NUMERIC(10,2)) AS [Average BMI],
       MIN(BMI) AS [Minimum BMI],
       CAST(STDEV(BMI) AS NUMERIC(10,2)) AS [Standard Deviation BMI]
FROM diabetes_young_adults_india
WHERE NOT Diabetes_Type = 'None'
GROUP BY Diabetes_Type
```

## 20. How many people have sleep hours less than 6 hours and also have high stress levels?
```sql
SELECT COUNT(*) AS [Total Count]
FROM diabetes_young_adults_india
WHERE Sleep_Hours < 6 AND Stress_Level > 6
```

## 21. How many people in each region have a sedentary lifestyle and moderate dietary habits?
```sql
SELECT Region, COUNT(*) AS [Total Count]
FROM diabetes_young_adults_india
WHERE Dietary_Habits LIKE 'moderate' AND Physical_Activity_Level LIKE 'sedentary'
GROUP BY Region
```

## 22. What is the average fasting blood sugar for individuals who smoke vs. those who don’t?
```sql
SELECT AVG(CASE WHEN Smoking = 1 THEN Fasting_Blood_Sugar END) AS [FBS Who Smoke],
       AVG(CASE WHEN Smoking = 0 THEN Fasting_Blood_Sugar END) AS [FBS Who Don't Smoke]
FROM diabetes_young_adults_india
```

## 23. How many individuals with a low genetic risk score have a higher BMI?
```sql
SELECT COUNT(*) AS [Individuals Count]
FROM diabetes_young_adults_india
WHERE Genetic_Risk_Score < 3 AND BMI > 28
```

## 24. How does the smoking status relate to the HbA1c levels of individuals?
```sql
SELECT Smoking, AVG(HbA1c) AS [Average HbA1c]
FROM diabetes_young_adults_india
GROUP BY Smoking
```

## 25. What is the average age of people with a family history of diabetes who also have Type 2 diabetes?
```sql
SELECT AVG(Age) AS [Average Age]
FROM diabetes_young_adults_india
WHERE Family_History_Diabetes = 1 AND Diabetes_Type = 'Type 2'
```

## 26 What are the common health conditions (fasting blood sugar, HbA1c, cholesterol) for people with moderate dietary habits?
```sql
select cast(avg(Fasting_Blood_Sugar)  AS NUMERIC(10,2))   [Average Blood Sugar] , 
	   cast( avg(HbA1c) AS NUMERIC(10,2))  [Average HbA1c], 
	  cast( avg(Cholesterol_Level) AS NUMERIC(10,2))  [Average Cholesterol Level]
from diabetes_young_adults_india
where Dietary_Habits = 'moderate'
```

## 27 What percentage of individuals with a BMI over 30 are from the North region?
```sql
select CAST((SELECT COUNT(*) FROM diabetes_young_adults_india where BMI > 30 AND Region like 'North' ) * 100.0 
/ COUNT(*) as Numeric(10,2) ) [% of BMI > 30 In North]
from diabetes_young_adults_india
where Region like 'North' 
```
## 28 How does alcohol consumption affect the cholesterol levels of individuals?
```sql
select
	avg(case when Alcohol_Consumption = 1 then Cholesterol_Level end) [Chol.. Level Who Drink Alcohol],
	avg(case when Alcohol_Consumption = 0 then Cholesterol_Level end) [Chol.. Level Who Don't Drink Alcohol]
from diabetes_young_adults_india
```
## 29 What is the distribution of family income for individuals with Type 1 and Type 2 diabetes?
```sql
select Diabetes_Type ,
CAST(avg(CAST(Family_Income AS DECIMAL(10,2))) AS NUMERIC(10,2)) [Average Income] , 
min(Family_Income) [Min Income] ,
max(Family_Income) [Maximum Salary]
from diabetes_young_adults_india
group by Diabetes_Type
```
## 30 What is the average sleep duration for individuals with high stress levels?
```sql
select CAST(avg(Sleep_Hours) AS NUMERIC(10,2)) [AVG Sleep]
from diabetes_young_adults_india
where Stress_Level > 6
```
