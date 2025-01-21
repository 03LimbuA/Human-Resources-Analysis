# Human-Resources-Analysis
This SQL data cleaning and analysis project, inspired by and following the tutorial of the Youtube Channel [Her Data Project](https://www.youtube.com/@herdataproject), looks at Human Resources data with over 22,000 rows (ranging from year 2000 - 2020). The dataset can be accessed [here](https://github.com/03LimbuA/Human-Resources-Analysis/blob/main/Human%20Resources2.csv).

The SQL queries for this project were written using MySQL Workbench. This project also includes the creation of a  HR dashboard, created using Tableau, providing a visual overview of essential metrics, including employee turnover, recruitment statistics, development progress, and workforce demographics - enabling HR professionals to effectively observe employee information. 

## The Analysis
The full analysis can be found in this [file]

The full list of queries used to aid the analysis can be found [here](https://github.com/03LimbuA/Human-Resources-Analysis/blob/main/HR%20SQL.sql)

EXAMPLE OF QUERIES:
```sql    
-- tenure distribution for each department (how long employees work in each department before they leave)
SELECT department, ROUND(AVG(DATEDIFF(CURDATE(), termdate)/365),0) as avg_tenure
FROM hr
WHERE termdate <= CURDATE() AND termdate <> '0000-00-00' AND age >= 18
GROUP BY department
