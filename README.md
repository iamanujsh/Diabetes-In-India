# Diabetes-In-India



# Diabetes Database Queries

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
