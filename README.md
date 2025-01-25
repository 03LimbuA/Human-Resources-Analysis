# Human-Resources-Analysis
This SQL data cleaning and analysis project, inspired by the Youtube Channel [Her Data Project](https://www.youtube.com/@herdataproject), looks at Human Resources data with over 22,000 rows (ranging from year 2000 - 2020). The dataset can be accessed [here](https://github.com/03LimbuA/Human-Resources-Analysis/blob/main/Human%20Resources2.csv).

The SQL queries for this project were written using MySQL Workbench. This project also includes the creation of a  [HR dashboard](https://public.tableau.com/app/profile/a.l5815/viz/HRdataanalysisDASHBOARD/Dashboard1?publish=yes), created using Tableau, providing a visual overview of essential metrics, including employee turnover, recruitment statistics, development progress, and workforce demographics - enabling HR professionals to effectively observe employee information. 

## The Analysis
The full analysis can be found in this [file](https://github.com/03LimbuA/Human-Resources-Analysis/blob/main/HR%20full%20analysis.pdf)

The full list of queries used to aid the analysis can be found [here](https://github.com/03LimbuA/Human-Resources-Analysis/blob/main/HR%20SQL.sql)

EXAMPLE OF QUERIES:
```sql    
-- tenure distribution for each department (how long employees work in each department before they leave)
SELECT department, ROUND(AVG(DATEDIFF(CURDATE(), termdate)/365),0) as avg_tenure
FROM hr
WHERE termdate <= CURDATE() AND termdate <> '0000-00-00' AND age >= 18
GROUP BY department
```

**Questions** 

1. the gender breakdown of employees in the company
2. the race/ethnicity breakdown of employees in the company
3. the age distribution of employees in the company
4. how many employees work at headquarters versus remote locations
5. the average length of employment for employees who've been terminated
6. the gender distribution variation across departments 
7. the distribution of job titles across the company
8. the department with the highest turnover rate
9. the distribution of employees across locations by state
10. how the company's employee count has changed over time based on hire and term dates
11. the tenure distribution for each department

**Analysis findings**
- There are more male employees than female and non-(gender)conforming employees
- The most dominant race amongst the employees is white, and the least dominant are American Indian and Native Hawaiian
- the youngest employee is 22, the oldest is 59
- the most dominant age group amongst the employees is 35-44, followed by 25-34 and 45-54; the least dominant age groups are 18-24 and 55-64
- around 75% of the employees work at headquarters, only around 25% work remotely
- the average length of employment for employees who've been terminated is around 8 years
- Fairly equal gender distribution across the departments; although, there seems to be more male employees than female employees across the departments
- Non-(gender)conforming employees are the least dominant gender across all the departments
-  highest turnover rate = Auditing department, with termination rate of 0.1800
- Marketing and Business Development have the lower turnover rates
- A large no. of employees are located in Ohio (81% of the employees)
- The net change in employees has increased from years 2000 – 2020 (by around 8%) –
over the years, terminations of employees have generally decreased, whilst around 1000 hires have been made per year
- the average tenure across all the departments (the length of time employees work for the department) seems to be around 8/9 years

IT IS IMPORTANT TO NOTE THAT:
- 967 records showed negative ages – we excluded these records in our analysis, we used ages above 18 in our SQL queries
- The dataset included termdates with dates into the future (e.g., 2066) – we excluded these records in our analysis, ensuring that we used termdates less than or equal to the current date, in our SQL queries

# Visualising the data
Using Tableau Public, I created a dashboard to display some of the findings from our anaysis. The dashboard can be accessed [here](https://public.tableau.com/app/profile/a.l5815/viz/HRdataanalysisDASHBOARD/Dashboard1?publish=yes).
![Image 21-01-2025 at 21 35](https://github.com/user-attachments/assets/c4f5a96c-f0e2-40db-823d-d0ca040f3028)


