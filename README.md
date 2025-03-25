# Overview ![Overview](3_Project\images\overview.png)

Welcome to my analysis of the data job market, focusing on data analyst roles in the United States. This project was born out of a desire to navigate and understand the job market more effectively using Python. It delves into the top-paying and in-demand skills to help find optimal job opportunities for data analysts.

The data is sourced from [Luke Barousse's Python Course](https://lukebarousse.com/python) which provides a foundation for my analysis. The data contains detailed information such as:
- job titles
- salary
- location and 
- essential skills for each role.  
Through a series of Python scripts, I explore key questions such as the most demanded skills, salary trends, and the intersection of demand and salary in data analytics.

# The Questions ❓

Using this project, I seek to answer the following questions:  
1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for data analysts to learn? (High Demand AND High Paying) 

# Tools Used 🛠️

For a deep dive into the dataset, I harnessed the power of several key tools such as:

- **Python ![python image](3_Project\images\python.png) :** This tool was the backbone of my analysis, allowing me to analyze the data and find critical insights. The following Python libraries were utilised:
    - **Pandas Library:** This was used to analyze the data. 
    - **Matplotlib Library:** This was used to visualize the data.
    - **Seaborn Library:** Helped me create more advanced visuals. 
- **Visual Studio Code ![VS code image](3_Project\images\visual-studio-code.png) :** My go-to IDE for writing and executing Python scripts.
- **Git & GitHub ![Git image](3_Project\images\git.png) :** Essential for version control, sharing my Python code and analysis, and ensuring collaboration and project tracking.

# Data Preparation and Cleanup 🛁

This section outlines the steps taken to prepare the data for analysis, ensuring accuracy and usability.

## Import & Clean Up Data

I start by importing necessary libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality.

```python
# Importing Libraries
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt  

# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])  #to cast the date from string to datetime type
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)  #to cast the skills from a string list to a list

```

## Filter US Jobs

To focus my analysis on the U.S. job market, I apply filters to the dataset, narrowing down to roles based in the United States.
This was a data driven decision as the dataset contains more information about the USA job market.

```python
#to decide on which country to focus my analysis, I ploted top 10 countries
df_country = df['job_country'].value_counts().head(10).to_frame()

sns.barplot(data=df_country, x= 'count', y= 'job_country',hue= 'count', palette= 'dark:green_r', legend= False )
sns.despine()
ax= plt.gca()
ax.xaxis.set_major_formatter(plt.FuncFormatter(lambda x, pos: f"{int(x/1000)}K"))
plt.xlabel('Number of Job')
plt.ylabel('')
plt.title('Top 10 Countries by Job Postings')
plt.show()
```

![Bar Chart of Job Postings Location](3_Project\images\country_chart.png)

```python
df_US = df[(df['job_country'] == 'United States')].copy()  #filter out the data for only the US market. This would be my final dataset

```

# Exploratory Data Analysis EDA 🔍

Exploring the dataset for the USA job market and Data Analyst roles only.  
This can be found here: [1_EDA](1_EDA.ipynb).

```python
df_DA_USA = df[(df['job_country'] == 'United States') & (df['job_title_short'] == 'Data Analyst')].copy()  #analysing only USA jobs as USA has the most jobs in the posting from below
```

```python
df_DA_USA.info()
```
| # | Column                    | Non-Null Count | Dtype              | 
|---|---------------------------|----------------|--------------------|
| 0 | job_title_short           | 67,816         | object             |
| 1 | job_title                 | 67,816         | object             |
| 2 | job_location              | 67,582         | object             | 
| 3 | job_via                   | 67,809         | object             | 
| 4 | job_schedule_type         | 67,107         | object             |
| 5 | job_work_from_home        | 67,816         | bool               |
| 6 | search_location           | 67,816         | object             |
| 7 | job_posted_date           | 67,816         | datetime64[ns]     |
| 8 | job_no_degree_mention     | 67,816         | bool               |
| 9 | job_health_insurance      | 67,816         | bool               |
| 10| job_country               | 67,816         | object             |
| 11| salary_rate               | 8,442          | object             |
| 12| salary_year_avg           | 4,350          | float64            |
| 13| salary_hour_avg           | 3,980          | float64            |
| 14| company_name              | 67,816         | object             |
| 15| job_skills                | 57,391         | object             |
| 16| job_type_skills           | 57,391         | object             |

- Total entries: 67,816
- Total columns: 17
- Memory usage: 8.0+ MB  


```python
df_DA_USA.describe()
```
|                  | Job Posted Date        | Salary Year Avg | Salary Hour Avg |
|------------------|------------------------|-----------------|-----------------|
| Count           | 67,816                 | 4,350           | 3,980           |
| Mean            | 2023-06-15 03:16:27    | $94,553.96      | $38.31          |
| Minimum         | 2023-01-01 00:00:04    | $25,000.00      | $8.00           |
| 25th Percentile | 2023-03-12 18:02:09    | $71,262.50      | $24.00          |
| Median (50%)    | 2023-06-11 18:49:23    | $90,000.00      | $33.00          |
| 75th Percentile | 2023-09-08 19:01:58    | $112,500.00     | $50.00          |
| Maximum         | 2023-12-31 23:31:54    | $375,000.00     | $391.00         |
| Standard Dev.   | N/A                    | $33,248.65      | $18.76          |


# The Analysis

Each notebook in this project file is aimed at investigating specific aspects of the data job market. Here’s how I approached each question:

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the most demanded skills for the top 3 most popular data roles. I filtered out those positions by which ones were the most popular, and got the top 5 skills for these top 3 roles. This query highlights the most popular job titles and their top skills, showing which skills should be focused on depending on the targeted role. 

View my notebook with detailed steps here: [2_Skill_Demand](2_Skills_count.ipynb).

### Visualize Data

```python
fig, ax = plt.subplots(len(top_titles), 1)
for i, job_title in enumerate(top_titles):
    df_plot = df_skills_percent[df_skills_percent['job_title_short'] == job_title].head()
    sns.barplot(df_plot, x= 'skill_percent', y= 'job_skills', ax= ax[i], hue= 'skill_percent', palette= 'dark:green_r', legend= False)
    ax[i].set_ylabel('')
    ax[i].set_xlabel('')
    ax[i].set_title(job_title)
    ax[i].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'{int(x)}%'))
    ax[i].set_xlim(0, 75)
    if i != len(top_titles) - 1:
        ax[i].set_xticks([])

    for index, val in enumerate(df_plot['skill_percent']):
        ax[i].text(val, index, f'{val:.0f}%', va= 'center')
fig.suptitle('Likelihood of Skills Requested for Top Job Titles in the United States', fontsize= 15)
fig.tight_layout(h_pad= 0.5)
```

### Results

![Likelihood of Skills Requested for Top Job Titles in the United States](3_Project\images\skill_likelihood.png)
  
*Bar chart visualizing the skills required for the top 3 data roles in the USA*

### Insights:

- SQL is the most requested skill for Data Analysts and Data Engineers, as it is required in over half the job postings for both roles. For Data Scientists, Python is the most sought-after skill, appearing in 72% of job postings.
- Data Engineers require more specialized technical skills (AWS, Azure, Spark) compared to Data Analysts and Data Scientists who are expected to be proficient in more general data management and analysis tools (Excel, Tableau).
- Python is a versatile skill, highly demanded across all three roles, but most prominently for Data Scientists (72%) and Data Engineers (65%) with 27% of Data Analysts jobs requiring it.


