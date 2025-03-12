# Salary Analysis
The project contains simple end to end data analysis made with mySQL and PowerBI.

* data was taken from salary_data dataset from Kaggle. https://www.kaggle.com/datasets/mohithsairamreddy/salary-data

## Processing the data
1. Imported the dataset to mySQL workbench
  
2. Updated column names using mySQL alter table tool
 
3. Cleaning inconsistent fields in 'Education_level' column
```sql
UPDATE salary.salary_data
SET Education_level = CASE 
    WHEN Education_level = 'Bachelor''s Degree' THEN 'Bachelor''s'
    WHEN Education_level = 'Master''s Degree' THEN 'Master''s'
    ELSE Education_level  
END;
```
4. Filtering out job titles with less than 10 entries to a new table for more accurate analysis 
```sql
CREATE TABLE salary.salary_data_filtered AS 
SELECT * 
FROM salary.salary_data 
WHERE job_title IN (
    SELECT job_title 
    FROM salary.salary_data 
    GROUP BY job_title 
    HAVING COUNT(job_title) >= 10
);
```
5. Cleaning the data by deleting rows with unrealistic values found in 'salary' column
```sql
DELETE FROM salary.salary_data_filtered WHERE salary < 1000
;
```
## Data analysis with SQL
1. Finding average salary values by job title and gender
```sql
SELECT 
	Job_title,
	gender,
	ROUND(AVG(salary), 0) AS average_salary
FROM salary_data_filtered
WHERE 
	Job_title = 'Data Analyst' OR
    	Job_title = 'Junior Sales Associate' OR
    	Job_title = 'Software Engineer'
   
GROUP BY Job_title, gender
ORDER BY Job_title
;
```
2. Finding 5 best/worst paid jobs across the dataset
```sql
   SELECT 
	ROUND(avg(salary),0) as average_salary,
	job_title
FROM salary_data_filtered
GROUP BY Job_title
ORDER BY average_salary desc -- (asc)
LIMIT 5
```
3. Percentage gender distribution of employees across the dataset
 ```sql
SELECT 
	gender,
	ROUND(COUNT(*) / (
		SELECT COUNT(*)
		FROM salary_data_filtered
        WHERE gender NOT LIKE 'other') * 100, 2)
    AS percentage_employed
FROM salary_data_filtered
WHERE gender NOT LIKE 'other'
GROUP BY gender 
  ```
## Data visualization using PowerBi tool

I've chosen po pick PowerBI to create short and comprahensive dashboard describing my findings. 
Dashboard contains bar charts about the best/worst paying jobs, scatter chart describing correlation of experience and salary,
combination of donut and tornado chart to show data regarding genders and a summary card 
