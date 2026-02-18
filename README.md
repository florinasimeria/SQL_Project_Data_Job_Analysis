# Introduction
Dive into the data job market! Focusing on Data Analyst role, this priject explores top-paying jobs, in-demand skills, and where high-deman meets high salary in data analitycs.
SQL queries? Check them out here: [project_sql folder](/project_sql/)
# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from a desire to pin-point top-payied and in-demand skills, streamlining others work to find optimal jobs.
Data hails from my [SQL course](https://lukebarousse.com/sql). It's packed with insights on job title, salaries, locations, and essential skills.
### The questions I wanted to answer trough my SQL queries were:
1. What are the top-paying jobs for my role?
2. What are the skills required for these top-paying roles?
3.What are the most in-demand skills for my role?
4. What are the top skills based on salary for my role?
5. What are the most optimal skills to learn?
# Tools I used
For my deep dive into the data analyst job market, I harnessed the power of several key tools:
- **SQL:** The backbone of my analysis, alowing me to query the database and unearth critical insights.
- **PostgreSQL:** The chosen database management system, ideal for handling the job posting data.
- **Visual Studio Code:** My go-to for database management and executing SQL queries.
- **Git and GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.
# The Analysis
Each query fro this project aimed at investigating specific aspects of the data analyst job market.
Here's how I approached each question:
## 1. Top Paying Data Analyst job
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs.Ths query highlights the high paying opprtunities in the filed.
```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
    job_title_short = 'Data Analyst' AND 
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10;
```
Here's the breackdown of the top analyst jobs in 2023:
- **Wide salary range:** Top 10 paying data analist roles span from $184,000 to $650,000, indicating significant salary potential in the filed.
-**Diverse Employers:** Companies like SmartAsset, Meta, and AT&T are among those offering high salary, showing a broad interest across differenet industries.
-**Job Title Variety:** There's a high diveristy in job titles, from Data Analyst to Director of Analytics, reflecting varied roles and specialisations within data analytics.
![Top Paying Roles](assets_1\Top_paying_roles.PNG)

*Bar graph visualizing the salary for the top 10 salaries for data analysts; Chat GPT generated this graph from my SQL  query results*
## 2.Skills for Top Paying Jobs
To undertand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation role.
```sql
WITH top_paying_jobs AS (
SELECT
    job_id,
    job_title,
    salary_year_avg,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
    job_title_short = 'Data Analyst' AND 
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```
Here's the breackdown of the most demanded skills for the top 10 highest paying data analyst jobs in 2023.
- **SQL** is leading with a bold count of 8.
- **Python** follows closely with a bold count of 7.
- **Tableau** is also highly sought after, with a bold count of 6. Other skills like **R**, **Snowflake**, **Pandas**, and **Excel**show varying degrees of demand.

![Top Paying Roles](assets_1\top_10_skills_in_top_paying_data_analyst_roles.PNG)

*Bar graph visualizing the count of skills for the top 10 paying jobs for data analysts; Chat GPT generated this graph from my SQL query results.
## 3. In-demand Skills for Data Analysts
This query helped indentify the skills most frequently requested in job postings, directing focus in areas with high demand.
```sql
SELECT 
    skills,
    COUNT (skills_job_dim.skill_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```
Here's a breakdown of the most demanded skills for data analysts in 2023.
**SQL** and **Excel** remain fundamental, emphasizing the need for strong foundationla skills in data processing and spreadsheet manipulation.
**Programming** and **Visualization Tools** like **Python**, **Tableau** and **Power BI** are essential, pointing towoards the increassing importance of technical skills in data storytelling and decision support.

![Skills_demand](assets_1\skills_and_demand_count.PNG)

*Table of the demand 5 skills in data analist job postings*
## 4. Skills Based on Salary
Exploring the average salaries associated with different skills revealed which are the highest paying.
```sql
SELECT 
    skills,
   ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 10;
```
Here's the breakdown of the results for top paying skills for Data Analysts:
- **High Demand for Big Data & ML Skills:** Top salaries are commanded by analysts skilled in big data technologies (PySpark, Couchbase), machine learning tools (DataRobot, Jupyter), and Python libraries (Pandas, NumPy), reflecting the industry's high valuation of data processing and predictive modeling capabilities.
- **Software Developement & Deployement Proficientcy:** Knowledge in developement and deployment tools (GitLab, Kubernetes, Airflow) indicates a lucrative crossoverd between data analysis and engineering, with a premium on skills that facilitate automation and efficient data pipeline management.
-**Cloud Computing Expertise:** Familiarity with cloud and data engineering tools (Elasticsearch, Databriks,GCP) underscores the growing importance of cloud-based analytics environments, sugesting that cloud proficiecny significantly boosts earning potential in data analytics.

![Skills_avg_salary](assets_1\skills_and_avg_salary.PNG)

*Table of the average salary for the top 10 paying skills for data analists*
## 5. Most Optimal Skills to Learn
Combining insights from demand and salary data this query aimed to pinpoint skills that are both in high demand and having high salaries, offering a strategic focus for skill development.
```sql
SELECT 
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT (skills_job_dim.skill_id) AS demand_count,
   ROUND(AVG(salary_year_avg), 2) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY 
    skills_dim.skill_id
    HAVING
    COUNT (skills_job_dim.skill_id) > 10
ORDER BY
      avg_salary DESC,
      demand_count DESC
LIMIT 10;
```
![Optimal_skills](assets_1\optimal_skills.PNG)

*Table of the most optimal skills for data analyst sorted by salary*
Here's a breakdown for the most optimal skills for Data Analyst in 2023:
- **High-Demand Programming Languages:** Phyton and R stand out for theyr high demand, with demand counts of 236 and 148 respectively.Despite theyr high demand, theis average salaries are arround $101,397 for Python and $100,499 for R, indicating that proficiecy in ths languages is highly valued but also videly available.
- **Cloud Tools and Tchnologies:** Skills in specialized technologies such as Snowflake, Azure, AWS, and BigQuery show significand demand with relatively high average salaries, pointing towards the growing importance of cloud platforms and big data technologies in data analysis.
- **Database Technologies:** The demand for skills in traditional and NoSQL database (Oracle, SQL Server, NoSql) with average salaries ranging from $97,786 to $104,534, reflects the enduring need for data storage retrieval, and management expertise.
# What I Learned
Throughout this adventure, I've turbocharged my SQL Tool kit with some serious firepower:
- ** Complex Query crafting:** mastered the art of advanced SQL maging tables like a pro and welding WITH clauses for ninja-level tem table maneuvers.
- **Data Aggregation:** Got cozy with GROUP BY and turned aggregate functions like COUNT() and AVG () into my data-suumarizing sidekicks.
- **Analutical Wizardry:** Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.
# Conclusions

## Insights 
1. **Top payng Data Analyst Job**: The highest-paying jobs for data analyst that allow 
remote work offer a wide range of salaries, the highest at $650,000!
2 **Skills for Top Payng Jobs**:High_paying Data Analyst jobs requie advanced proficiency in SQL, suggesting it's a critical skill for earning a top salary.
3. **Most In-Demand Skills**: SQL  is also th most demanded skill in the data analyst job market, thus making it essentials for job seekers.
4. **Skills with Higher Salaries**: Specialized skills, such as SVN and Solidity, are assiciated with highest average salaries, indicating a remium on niche expertize.
5. **Optimal Skills for Job Market Value**:SQL leds in demand and offers for high average salary, possitioning it as one of the most optimal skills for data analyst to learn to maximize the market value.
### Closing Thoughts
This Project enhanced my SQL skills and provided valuable insights into the data analyst job market.The findings for the analysis serve asa guide to prioritizing skill development and job serch efforts. Aspiring data analysts can better position themselves in a comptitive job market by focusing on high-demand, high_salary skills. This exploration highlights the importance of continuaous learning and adaption to emerging trends in the filed of data analytics.

