# Diabetes Data Analysis

This project focuses on analyzing a diabetes dataset to uncover key insights and trends using SQL. The dataset contains various features like age, BMI, insulin levels, glucose concentration, physical activity, dietary habits, and family history. The analysis aims to identify patterns in diabetes occurrence, risk factors, and patient demographics. By applying SQL queries, we can derive useful insights to understand how different factors contribute to diabetes and the effectiveness of treatments.

## Dataset Overview

The dataset consists of health-related data for young adults in India, including information about their diabetes status (Type 1, Type 2, or none), physical activity levels, dietary habits, family history, and other health metrics like cholesterol, HbA1c, and fasting blood sugar. The data provides an opportunity to identify trends related to diabetes prevalence, risk factors, and lifestyle choices in the population.

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
Insight: This query helps identify if there are regional differences in the average BMI, which may indicate varying lifestyle or dietary habits across different parts of India.

## 2. How many people have Type 1 diabetes vs. Type 2 diabetes?
```sql
SELECT Diabetes_Type, COUNT(DISTINCT id) AS [Total Patient]
FROM diabetes_young_adults_india
WHERE NOT Diabetes_Type = 'None'
GROUP BY Diabetes_Type
```
Insight: This query compares the prevalence of Type 1 vs. Type 2 diabetes, which can guide healthcare strategies and awareness campaigns tailored to each type.

## 3. What are the top 5 individuals with the highest cholesterol levels?
```sql
SELECT TOP 5 *
FROM diabetes_young_adults_india
ORDER BY Cholesterol_Level DESC
```
Insight: Identifying individuals with the highest cholesterol levels can help target those at a greater risk of heart disease, especially among those already dealing with diabetes.

## 4. What is the average HbA1c for people with prediabetes?
```sql
SELECT AVG(HbA1c) AS [AVG_HbA1c]
FROM diabetes_young_adults_india
WHERE Prediabetes = 1
```
Insight: Analyzing HbA1c levels in prediabetic individuals helps assess how close they are to developing Type 2 diabetes, highlighting the importance of early intervention.

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
Insight: This query examines the correlation between lifestyle (active vs. sedentary) and smoking habits. Smoking can worsen the effects of a sedentary lifestyle, and this query helps uncover that relationship.
## 6. What is the average stress level for each region?
```sql
SELECT Region, AVG(Stress_Level) AS [Average Stress Level]
FROM diabetes_young_adults_india
GROUP BY Region
```
Insight: Stress levels vary by region, and this analysis can guide mental health interventions and stress management programs, particularly in high-stress areas.
## 7. Which individuals are sedentary and have unhealthy dietary habits?
```sql
SELECT *
FROM diabetes_young_adults_india
WHERE Physical_Activity_Level = 'Sedentary' AND Dietary_Habits = 'Unhealthy'
```
Insight: Identifying individuals with both sedentary lifestyles and poor dietary habits can highlight at-risk groups for diabetes, enabling targeted health interventions.
## 8. What is the average family income for individuals with Type 2 diabetes?
```sql
SELECT Diabetes_Type, CAST(AVG(CAST(Family_Income AS DECIMAL(18,2))) AS NUMERIC(18,2)) AS [Average Family Income]
FROM diabetes_young_adults_india
WHERE Diabetes_Type = 'Type 2'
GROUP BY Diabetes_Type
```
Insight: This analysis explores the relationship between socio-economic status and Type 2 diabetes. A lower average family income may correlate with less access to healthcare and healthier food options.
## 9. What percentage of people in each region have prediabetes?
```sql
SELECT Region,
       CAST((COUNT(CASE WHEN Prediabetes = 1 THEN 1 END) * 100.0 / COUNT(*)) AS NUMERIC(10, 2)) AS [% of Prediabetes / Region]
FROM diabetes_young_adults_india
GROUP BY Region;
```
Insight: Understanding the prevalence of prediabetes by region helps identify areas that need more diabetes prevention programs and early detection strategies.
## 10. Who are the individuals under 18 with Type 1 diabetes?
```sql
SELECT *
FROM diabetes_young_adults_india
WHERE Age < 18 AND Diabetes_Type = 'Type 1'
```
Insight: This query explores how varying levels of screen time correlate with stress levels. Extended screen time could contribute to higher stress, affecting overall health.
## 11. What is the average fasting blood sugar for people with Type 2 diabetes?
```sql
SELECT Diabetes_Type, CAST(AVG(Fasting_Blood_Sugar) AS NUMERIC(10,2)) AS [AVG Fasting Blood Sugar]
FROM diabetes_young_adults_india
GROUP BY Diabetes_Type
HAVING Diabetes_Type = 'Type 2'
```
Insight: This query helps determine the effect of alcohol consumption on cholesterol levels, which is an important factor in diabetes management.

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
Insight: Physical activity often reduces stress, so this query helps assess the mental health benefits of different activity levels.
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
Insight: Physical activity often reduces stress, so this query helps assess the mental health benefits of different activity levels.

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
Insight: Analyzing fasting blood sugar levels for each diabetes type provides insight into how well individuals with different types of diabetes are managing their condition.
## 14. How many individuals with a family history of diabetes have diabetes themselves?
```sql
SELECT COUNT(*) AS [Total Diabetes Count Based On Family History]
FROM diabetes_young_adults_india
WHERE Family_History_Diabetes = 1 AND Diabetes_Type IN ('Type 1', 'Type 2')
```
Insight: This query reveals whether gender plays a role in the prevalence of Type 1 vs. Type 2 diabetes in the dataset.


## 15. What is the average family income for people with different levels of physical activity?
```sql
SELECT Physical_Activity_Level, CAST(AVG(CAST(Family_Income AS DECIMAL(10,2))) AS NUMERIC(10,2)) AS [Avg Family Income]
FROM diabetes_young_adults_india
GROUP BY Physical_Activity_Level
```
Insight: Understanding the age group that is most affected by diabetes can help target educational programs and healthcare interventions.


## 16. How many individuals have a high cholesterol level (e.g., above 240)?
```sql
SELECT COUNT(*) AS [Total Individual]
FROM diabetes_young_adults_india
WHERE Cholesterol_Level > 240
```
Insight: This query helps analyze the potential impact of vegetable consumption on BMI, which is an important indicator for diabetes management.


## 17. What is the average age of individuals who consume fast food regularly?
```sql
SELECT AVG(Age) AS [Average Age]
FROM diabetes_young_adults_india
WHERE Dietary_Habits = 'Unhealthy' AND Fast_Food_Intake > 4
```
Insight: Knowing the prevalence of a family history of diabetes helps identify those at higher risk for developing diabetes and provides insight into genetic factors.


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
Insight: This query helps identify the relationship between smoking and Type 2 diabetes, which could have implications for prevention programs.


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
Insight: Physical activity plays a key role in reducing stress. This query shows how different activity levels impact mental well-being.


## 20. How many people have sleep hours less than 6 hours and also have high stress levels?
```sql
SELECT COUNT(*) AS [Total Count]
FROM diabetes_young_adults_india
WHERE Sleep_Hours < 6 AND Stress_Level > 6
```
Insight: This query helps understand which age groups are most prone to different types of diabetes.


## 21. How many people in each region have a sedentary lifestyle and moderate dietary habits?
```sql
SELECT Region, COUNT(*) AS [Total Count]
FROM diabetes_young_adults_india
WHERE Dietary_Habits LIKE 'moderate' AND Physical_Activity_Level LIKE 'sedentary'
GROUP BY Region
```
Insight: This query provides insights into insulin requirements for people with Type 1 diabetes.


## 22. What is the average fasting blood sugar for individuals who smoke vs. those who don’t?
```sql
SELECT AVG(CASE WHEN Smoking = 1 THEN Fasting_Blood_Sugar END) AS [FBS Who Smoke],
       AVG(CASE WHEN Smoking = 0 THEN Fasting_Blood_Sugar END) AS [FBS Who Don't Smoke]
FROM diabetes_young_adults_india
```
Insight: Analyzing sleep patterns and their correlation with glucose levels can help understand how rest affects blood sugar regulation.


## 23. How many individuals with a low genetic risk score have a higher BMI?
```sql
SELECT COUNT(*) AS [Individuals Count]
FROM diabetes_young_adults_india
WHERE Genetic_Risk_Score < 3 AND BMI > 28
```
Insight: This query identifies how many older individuals have Type 1 diabetes, which is usually diagnosed earlier in life, helping to understand late-onset cases.


## 24. How does the smoking status relate to the HbA1c levels of individuals?
```sql
SELECT Smoking, AVG(HbA1c) AS [Average HbA1c]
FROM diabetes_young_adults_india
GROUP BY Smoking
```
Insight: Identifying the overlap of Type 2 diabetes and high cholesterol can guide healthcare providers in offering comprehensive care for both conditions.


## 25. What is the average age of people with a family history of diabetes who also have Type 2 diabetes?
```sql
SELECT AVG(Age) AS [Average Age]
FROM diabetes_young_adults_india
WHERE Family_History_Diabetes = 1 AND Diabetes_Type = 'Type 2'
```
Insight: This query shows regional variations in glucose levels, which may highlight areas with higher or lower diabetes risk.


## 26 What are the common health conditions (fasting blood sugar, HbA1c, cholesterol) for people with moderate dietary habits?
```sql
select cast(avg(Fasting_Blood_Sugar)  AS NUMERIC(10,2))   [Average Blood Sugar] , 
	   cast( avg(HbA1c) AS NUMERIC(10,2))  [Average HbA1c], 
	  cast( avg(Cholesterol_Level) AS NUMERIC(10,2))  [Average Cholesterol Level]
from diabetes_young_adults_india
where Dietary_Habits = 'moderate'
```
Insight: This query helps assess the proportion of people with Type 1 diabetes who are following recommended management protocols.


## 27 What percentage of individuals with a BMI over 30 are from the North region?
```sql
select CAST((SELECT COUNT(*) FROM diabetes_young_adults_india where BMI > 30 AND Region like 'North' ) * 100.0 
/ COUNT(*) as Numeric(10,2) ) [% of BMI > 30 In North]
from diabetes_young_adults_india
where Region like 'North' 
```
Insight: This query highlights the number of individuals at risk due to unhealthy eating habits, contributing to the prevalence of diabetes.


## 28 How does alcohol consumption affect the cholesterol levels of individuals?
```sql
select
	avg(case when Alcohol_Consumption = 1 then Cholesterol_Level end) [Chol.. Level Who Drink Alcohol],
	avg(case when Alcohol_Consumption = 0 then Cholesterol_Level end) [Chol.. Level Who Don't Drink Alcohol]
from diabetes_young_adults_india
```
Insight: Understanding the role of family history in Type 2 diabetes can highlight genetic risk factors in the population.


## 29 What is the distribution of family income for individuals with Type 1 and Type 2 diabetes?
```sql
select Diabetes_Type ,
CAST(avg(CAST(Family_Income AS DECIMAL(10,2))) AS NUMERIC(10,2)) [Average Income] , 
min(Family_Income) [Min Income] ,
max(Family_Income) [Maximum Salary]
from diabetes_young_adults_india
group by Diabetes_Type
```
Insight: Smoking can contribute to increased BMI and exacerbate diabetes symptoms. This query helps identify the impact of smoking on weight management.


## 30 What is the average sleep duration for individuals with high stress levels?
```sql
select CAST(avg(Sleep_Hours) AS NUMERIC(10,2)) [AVG Sleep]
from diabetes_young_adults_india
where Stress_Level > 6
```
Insight: This query helps identify how age impacts BMI, which is an important factor in the management of diabetes.

## Business Implications
Targeted Health Programs: Based on regional variations in diabetes prevalence, lifestyle habits, and stress levels, healthcare organizations can design region-specific programs that focus on improving physical activity, diet, and mental well-being.
Awareness Campaigns: The findings related to smoking, alcohol consumption, and sedentary lifestyles highlight the need for public awareness campaigns about their impacts on diabetes risk.
Preventative Measures: Early identification of prediabetes in specific regions can lead to the implementation of diabetes prevention programs, targeting at-risk groups for early intervention and better management.

