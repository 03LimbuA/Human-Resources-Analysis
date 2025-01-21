# Human-Resources-Analysis

shows data cleaning and analysis in SQL

## The Queries
```sql
CREATE DATABASE projects;

USE projects;

SELECT * FROM hr;
-- DATA CLEANING ---------------------------------------------------------

ALTER TABLE hr
CHANGE COLUMN id emp_id VARCHAR(20) NULL;

-- checking data types for columns
DESCRIBE hr;

-- change the data type for birthdate 
SELECT birthdate FROM hr;
-- we find inconsistent formatting in birthdate column, so we fix that
UPDATE hr
SET birthdate = CASE 
WHEN birthdate LIKE '%/%' THEN date_format(str_to_date(birthdate, '%m/%d/%Y'), '%Y-%m-%d')
    WHEN birthdate LIKE '%-%' THEN date_format(str_to_date(birthdate, '%m-%d-%Y'), '%Y-%m-%d')
    ELSE NULL
END;
-- we go back to changing the data type for birthdate alter
ALTER TABLE hr
MODIFY COLUMN birthdate DATE;
DESCRIBE hr; 

-- we change the data type for hire_date too
UPDATE hr
SET hire_date = CASE 
WHEN hire_date LIKE '%/%' THEN date_format(str_to_date(hire_date, '%m/%d/%Y'), '%Y-%m-%d')
    WHEN hire_date LIKE '%-%' THEN date_format(str_to_date(hire_date, '%m-%d-%Y'), '%Y-%m-%d')
    ELSE NULL
END;

ALTER TABLE hr
MODIFY COLUMN hire_date DATE;
DESCRIBE hr; 

SELECT hire_date FROM hr;

-- we change data type and formatting for termdate as well
SELECT termdate from hr;

SET sql_safe_updates = 0;

ALTER TABLE hr
MODIFY COLUMN termdate DATE;

UPDATE hr
SET termdate = date(STR_TO_DATE(termdate, '%Y-%m-%d %H:%i:%s UTC'))
WHERE termdate IS NOT NULL AND termdate != ' '

-- we add a new column for 'age'

ALTER TABLE hr ADD COLUMN age INT;  

UPDATE hr
SET age = timestampdiff(YEAR, birthdate, CURDATE());
SELECT birthdate, age FROM hr;

SELECT 
	min(age) AS youngest,
    max(age) AS oldest
FROM hr;

-- we see that youngest age is -44 (obviously can't be true), so we 
-- check how many records in our data set have value less than 18 (reasonable working age)

SELECT count(*) FROM hr WHERE age < 18;
-- we find 967 records with ages less than 18 - we can exclude these 967 records in our analysis

-- THE ANALYSIS ------------------------------------------------------------

-- the gender distribution of employees in the company 
-- 'termdate is NULL' means the employees still currently work at the company (no termdate)
SELECT gender,count(*) AS count
FROM hr
WHERE age >= 18 AND termdate is NULL
GROUP BY gender;

-- the race/ethnicity breakdown of employees in the company
SELECT race, COUNT(*) AS count
FROM hr
WHERE age >= 18 AND termdate is NULL
GROUP BY race
ORDER BY count(*) DESC;

-- age distribution of employees in the company
SELECT 
  MIN(age) AS youngest,
  MAX(age) AS oldest
FROM hr
WHERE age >= 18 AND termdate is NULL;
-- so youngest is 22, oldest is 59
SELECT 
  CASE 
    WHEN age >= 18 AND age <= 24 THEN '18-24'
    WHEN age >= 25 AND age <= 34 THEN '25-34'
    WHEN age >= 35 AND age <= 44 THEN '35-44'
    WHEN age >= 45 AND age <= 54 THEN '45-54'
    WHEN age >= 55 AND age <= 64 THEN '55-64'
    ELSE '65+' 
  END AS age_group, 
  COUNT(*) AS count
FROM hr
WHERE age >= 18 AND termdate is NULL
GROUP BY age_group
ORDER BY age_group;
-- next, we add gender to the column
SELECT 
  CASE 
    WHEN age >= 18 AND age <= 24 THEN '18-24'
    WHEN age >= 25 AND age <= 34 THEN '25-34'
    WHEN age >= 35 AND age <= 44 THEN '35-44'
    WHEN age >= 45 AND age <= 54 THEN '45-54'
    WHEN age >= 55 AND age <= 64 THEN '55-64'
    ELSE '65+' 
  END AS age_group, gender, 
  COUNT(*) AS count
FROM hr
WHERE age >= 18 AND termdate is NULL
GROUP BY age_group, gender
ORDER BY age_group, gender;

-- how many employees work at headquarters vs remote locations
SELECT location, COUNT(*) as count
FROM hr
WHERE age >= 18 AND termdate is NULL
GROUP BY location;

-- average length of employment for employees who've been terminated
SELECT ROUND(AVG(DATEDIFF(termdate, hire_date)),0)/365 AS avg_length_of_employment
FROM hr
WHERE termdate <= CURDATE() AND age >= 18;

-- gender distribution variation across departments
SELECT department, gender, COUNT(*) as count
FROM hr
WHERE age >= 18 AND termdate is NULL
GROUP BY department, gender
ORDER BY department;

-- distribution of job titles across the company
SELECT jobtitle, COUNT(*) as count
FROM hr
WHERE age >= 18 AND termdate is NULL
GROUP BY jobtitle
ORDER BY jobtitle DESC;

-- department with the highest turnover rate
SELECT department, COUNT(*) as total_count, 
    SUM(CASE WHEN termdate <= CURDATE() AND termdate <> NULL THEN 1 ELSE 0 END) as terminated_count, 
    SUM(CASE WHEN termdate is NULL THEN 1 ELSE 0 END) as active_count,
    (SUM(CASE WHEN termdate <= CURDATE() THEN 1 ELSE 0 END) / COUNT(*)) as termination_rate
FROM hr
WHERE age >= 18
GROUP BY department
ORDER BY termination_rate DESC;

-- distribution of employees across locations by state 
SELECT location_state, COUNT(*) as count
FROM hr
WHERE age >= 18 AND termdate is NULL
GROUP BY location_state
ORDER BY count DESC;

-- how the company's employee count has changed over time based on hire and term dates
SELECT 
    year, 
    hires, 
    terminations, 
    (hires - terminations) AS net_change,
    ROUND(((hires - terminations) / hires * 100), 2) AS net_change_percent
FROM (
    SELECT 
        YEAR(hire_date) AS year, 
        COUNT(*) AS hires, 
        SUM(CASE WHEN termdate <> '0000-00-00' AND termdate <= CURDATE() THEN 1 ELSE 0 END) AS terminations
    FROM 
        hr
    WHERE age >= 18
    GROUP BY 
        YEAR(hire_date)
) subquery
ORDER BY 
    year ASC;
    
-- tenure distribution for each department (how long employees work in each department before they leave)
SELECT department, ROUND(AVG(DATEDIFF(CURDATE(), termdate)/365),0) as avg_tenure
FROM hr
WHERE termdate <= CURDATE() AND termdate <> '0000-00-00' AND age >= 18
GROUP BY department
