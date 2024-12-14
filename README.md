# Introduction
  This project focused around using SQL queries to gather data about data related job postings in 2023 and skills assosiacted with these jobs. 
  
  I specifically looked at: 
  - The top paying jobs 
  - The skills assosciated with those jobs
  - The most in demand skills
  - The top paying skills 
  - The optimal skills for data jobs.

Here are the files with my SQL queries: [sql_files](/sql_%20files/)
# Background
I had now completed two Google Data Analytics certificates, but what was required of me to get a job? So I put my skills to use and gained insights from a dataset, being able to use SQL to not only help me understand my job market of interest, but also display my skills to my future employer was perfect for a project.

### The questions I wanted to answer using SQL were:

1. What are the top-paying jobs for my role?
2. What are the skills required for these top-paying roles?
3. What are the most in demand skills for my role?
4. What are the top skills based on salary for my role?
5. What are the most optimal skills to learn?

# The Tools I Used
During my exploration of the data I used these tools to draw my insights:

- **SQL:** Using SQL I wrote queries to answer each of my questions. I also used SQL to understand the dataset and the values within it, this is not included in my SQL files.
- **PostgreSQL:** The database management system chosen to hold the data.
- **Visual Studio Code:** The code editor I chose to execute my SQL queries.
- **Tableau:** The visualization software I used to create graphs to represent the data .

# The Analysis
The queries in this project focused on exploring the data job market and understanding the characteristics of the data. This is how I approached each question and formulated my query:

## 1. Top Paying Data Analyst Jobs
To locate the data I needed I filtered the data for location and average yearly salary specifically setting the location for London, Uk. This was the location I centered all my queries around as you will see throughout my project. I decided to join two tables together in order to get the company name included into my query results. These results gave me a clear understanding of the job market within my selected filters and therefore I could sort for the top paying data analyst roles.

```sql
SELECT 
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name as company_name
FROM
    job_postings_fact
LEFT JOIN
    company_dim ON
    job_postings_fact.company_id = company_dim.company_id
WHERE   
    (job_location LIKE '%UK%' AND job_location LIKE '%London%') AND
    job_title_short = 'Data Analyst' AND 
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
```
**Here are the insights I gained from this query:**

* **Large Salary Range:** The top 15 paying jobs have a salary range from £57,500 to £177,283. This highlights that there is a significant earning potential in the field.

* **Diverse Industries:** Many of these jobs are from companies I have never heard of, this has prompted me to research these. I have found major diversity within the industries of the job applications. This is important to me because I may have a choice to work in a specific industry I am interested in.

* **Variety in the Job Title:** I can see that the full job title of these postings are again very diverse which highlights to me the different levels of opportunities within the data field.


![alt text](<sql_project\Tableau Viz\Top Paying Jobs and their Skills in London 2023.png>)
*A simple bar graph showing the top 15 highest paying data analyst jobs in London in 2023 and their salaries. This visualization was created using tableau and my query results as a csv.*


## 2. Skills For the Top Paying Jobs

In order to understand what skills were assosiacted with each job I used the previous query, ammended it slightly and put it into a cte. I then joined the data within the cte to the skills data and filtered and sorted it.

```sql
WITH top_paying_jobs AS (
    SELECT 
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN
        company_dim ON
        job_postings_fact.company_id = company_dim.company_id
    WHERE   
        (job_location LIKE '%UK%' AND job_location LIKE '%London%') AND
        job_title_short = 'Data Analyst' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
)

SELECT 
    top_paying_jobs.*,
    skills
FROM
    top_paying_jobs
INNER JOIN
    skills_job_dim ON
    top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN
    skills_dim ON
    skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC
```

**The Insights into each skill I gained from this query are:**

**SQL, Excel, Python:** These skills the most prevalent among the top 15 highest paying jobs in London for 2023 with a count of 7.

**Tableau, Go:** These two skills come in the middle of skills count with a count of 4.

**Sheets, VBA, Word:** These skills come in at the lower end with a count of 2. However they are not the lowest.

These Insights were drawn from the SQL query results as well as the Tableau Visualization below.

![alt text](<sql_project/Tableau Viz/Top Paying Jobs and their Skills in London 2023.png>)
*A simple bar graph showing the top 10 highest skill count for the top 15 data analyst jobs in London for 2023.*


## 3. The most In-Demand Skills for Data Analyst Jobs
This query gave me similar results to the last, however this query finds the skill count for every data analyst job posting in London. These query was not restricted to the top 15 paying. This helped me to see what 5 skills are the most demanded within my job market of interest.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM
    job_postings_fact
INNER JOIN
    skills_job_dim ON
    job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN
    skills_dim ON
    skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    (job_location LIKE '%UK%' AND job_location LIKE '%London%') AND
    job_title_short = 'Data Analyst' 
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5 
```
**The insights gained from this query are:**

**SQL** and **Excel** remain the most demanded skills overall which emphasizes the needs for simple yet effective tools within the data analytics field.

**Programming** and **Visualization tools** like **Python**, **Tableau** and **Power BI** are essential to the process of analysing data and telling stories with data to drive important decision making.

I found that a table is most effective for sharing the query results.

| Skills     | Demand Count |
|------------|--------------|
| SQL        | 188          |
| Python     | 156          |
| Excel      | 100          |
| Tableau    | 73           |
| Power BI   | 68           |

*A table showing the results of the query.*

## 4. Skills Based on Average Salary

In this query I explored the skills and the average pay assosiacted with them, again filtering to London and the top 15 highest paying skills.

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM
    job_postings_fact
INNER JOIN
    skills_job_dim ON
    job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN
    skills_dim ON
    skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    (job_location LIKE '%UK%' AND job_location LIKE '%London%') AND
    job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 15
```
**The insights I gained from this query are:**

**Specialized Technical Skills Pay More:** Advanced tools like TensorFlow, PyTorch, and NoSQL lead to top salaries due to their demand in AI, machine learning, and big data roles.

**Versatility is important:** Python stands out as a versatile skill, valued across technical and business-focused roles, while soft skills like PowerPoint and GDPR compliance also boost employability.

**Cloud Skills Are Growing:** Tools like Azure reflect the increasing reliance on cloud-based analytics solutions.

**Legacy Tools haven't lost value:** Skills like SharePoint and MATLAB remain relevant in certain industries, complementing modern technologies.

**Communication and Compliance is vital:** Professionals who can effectively present insights and navigate regulatory environments (GDPR) are well-positioned for success.


| Skills       | Avg Salary ($) |
|--------------|----------------|
| Pandas       | 177,283        |
| TensorFlow   | 177,283        |
| C++          | 177,283        |
| PyTorch      | 177,283        |
| NumPy        | 177,283        |
| NoSQL        | 163,782        |
| JavaScript   | 111,175        |
| Python       | 110,353        |
| PowerPoint   | 105,838        |
| Azure        | 105,211        |
| DAX          | 105,000        |
| GDPR         | 105,000        |
| SharePoint   | 100,500        |
| Outlook      | 100,500        |
| MATLAB       | 98,500         |

## 5. Most Optimal Skills to Learn

This query aimed to pinpoint skills that are in both high demand and have high salaries by combining insights from demand and salary data. This gives me the best information in order to optimise my salary and skills simultaneously. 

```sql
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM
        job_postings_fact
    INNER JOIN
        skills_job_dim ON
        job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN
        skills_dim ON
        skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        (job_location LIKE '%UK%' AND job_location LIKE '%London%') AND
        job_title_short = 'Data Analyst' AND
        salary_year_avg IS NOT NULL
    GROUP BY
        skills_dim.skill_id
), average_salary AS (
    SELECT
        skills_job_dim.skill_id,
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM
        job_postings_fact
    INNER JOIN
        skills_job_dim ON
        job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN
        skills_dim ON
        skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        (job_location LIKE '%UK%' AND job_location LIKE '%London%') AND
        job_title_short = 'Data Analyst' AND
        salary_year_avg IS NOT NULL
    GROUP BY
        skills_job_dim.skill_id
)

SELECT
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM
    skills_demand
INNER JOIN
    average_salary ON 
    skills_demand.skill_id = average_salary.skill_id
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 15
```
**The most optimal skills to learn are:**

**Python** emerges as the best skill to learn, factoring in its high demand, strong salary, and broad applicability. It is widely used in data analytics, machine learning, and software development, offering versatility and job security.

Pairing Python with **Azure** or a machine learning library like **TensorFlow** or **PyTorch** can enhance career prospects further.

| Skill ID | Skills      | Demand Count | Avg Salary ($) |
|----------|-------------|--------------|----------------|
| 94       | NumPy       | 1            | 177,283        |
| 13       | C++         | 1            | 177,283        |
| 101      | PyTorch     | 1            | 177,283        |
| 99       | TensorFlow  | 1            | 177,283        |
| 93       | Pandas      | 1            | 177,283        |
| 2        | NoSQL       | 1            | 163,782        |
| 9        | JavaScript  | 1            | 111,175        |
| 1        | Python      | 7            | 110,353        |
| 196      | PowerPoint  | 2            | 105,838        |
| 74       | Azure       | 3            | 105,211        |
| 105      | GDPR        | 1            | 105,000        |
| 184      | DAX         | 1            | 105,000        |
| 198      | Outlook     | 1            | 100,500        |
| 195      | SharePoint  | 1            | 100,500        |
| 15       | MATLAB      | 1            | 98,500         |


# Conclusions

## Insights
These are the major insights gained from this analysis:

1. **Large Variety in Data Analyst Jobs:** The salaries, industries and job titles vary greatly for data analytics jobs which signifies a great amount of options within the job market.

2. **SQL, Excel and Python:** These skills seem to be the most prevelant among the top 15 paying data analyst jobs. Signifiying overall the top paying jobs request these skills the most.

3. **The Most Demanded Skills Overall:** The most demanded skills for all data analyst jobs in London are **SQL**, **Python**, **Excel**, **Tableau**, and **Power BI** for 2023.

4. **Machine Learning and Big Data Skills Pay More:** Skills like **Pandas**, **Tensorflow**, and **C++** lead to higher paying roles due to their advanced characteristics and specialized use.

5. **Python is the Most Optimal Skill:** Python is the most optimal skill to learn for salary and demand, although other skills pay more but have a lower demand and vice versa Python is optimal.

## Closing Thoughts 

This Project Helped me practice and showcase my SQL skills while also growing my knowledge of the data job market. In doing this project I have clear understanding of the skills needed and average salaries for different factors. Hopefully this project conveys my ability to perform simple and advanced queries to retrieve and gain insights on data.

***This project was performed using a precleaned dataset created by [Luke Barousse](http://lukebarousse.com/)***
