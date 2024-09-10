Exercises from data scientist Luke Barousse' SQL Course on Youtube: 
[SQL for Data Analytics](https://www.youtube.com/watch?v=7mz73uXD9DA)


## Question 1
**What are the top-paying data analyst jobs?**
- Identify the top 10 highest-paying Data Analyst roles that are available remotely.
- Focuses on job postings with specified salaries (remove nulls).
- Why? Highlight the top-paying opportunities for Data Analysts, offering insights into employee options and location flexibility.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    name AS company_name,
    job_schedule_type,
    salary_year_avg,
    job_posted_date
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
```

## Question 2
**What skills are required for the top-paying data analyst jobs?** 
- Use the top 10 highest-paying Data Analyst jobs from first query
- Add the specific skills required for these roles
- Why? It provides a detailed look at which high-paying jobs demand certain skills,
    helping job seekers understand which skills to develop that align with top salaries.

```sql
WITH top_10_jobs AS (
SELECT
    job_id,
    job_title,
    name AS company_name,
    salary_year_avg
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

SELECT top_10_jobs.*,
    skills_dim.skills
FROM top_10_jobs
INNER JOIN skills_job_dim ON skills_job_dim.job_id = top_10_jobs.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```

## Question 3
**What are the most in-demand skills for data analysts?**
- Join job postings to inner join table similar to query 2
- Identify the top 5 in-demand skills for a data analyst.
- Focus on all job postings.
- Why? Retrieves the top 5 skills with the highest demand in the job market, 
    providing insights into the most valuable skills for job seekers.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS skill_demand
FROM job_postings_fact
INNER JOIN skills_job_dim ON skills_job_dim.job_id = job_postings_fact.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Analyst'
GROUP BY 
    skills
ORDER BY 
    skill_demand DESC
LIMIT 5;
```
