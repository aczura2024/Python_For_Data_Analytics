# Overview:

Welcome to my analysis of the data job market, where I focus on data analyst roles. This project was created with a two-fold intention: a desire to improve my Python skills for data analysis and navigate the data analytics job market more effectively. It explores the top-paying and in-demand skills to help find the optimal job opportunities. 

The data is sourced from Luke Barouuse's Python Course which lays the bedrock for my analsis. It contains detailed information on job titles, salaries, locations and essential skills. With a series of Python scripts, I will uncover the answers to questions such as the most demanded skills, salary trends and the intersection of demand and salary in data analytics. 


# The Questions

Below are the questions I am answering in my project:

1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for analysts to learn? (High Demand AND High Paying)


# Tools I Used
For my deep dive into the data analyst job market, I utilized several powerful tools:

* **Python**: This language allows me to analyze the data and uncover important insghts. I utilized the following Python libraries:
    * **Pandas Library**: Used for analyzing the data.
    * **Matplotlib Library**: Used for visualizing the data.
    * **Seaborn LIbrary**: Used for more advanced, customized visualizations.
* **Jupyter Notebooks**: The tool I used to run my Python scripts which made organizing my code and output simple.
* **Visual Studio Code**: The software I used to execute my Python scripts.
* **Git & GitHub**: Essential for version control and sharing my Python code and analysis, ensuring collaboration and project tracking.

# Data Preparation and Cleaning
This section outlines the steps I took to prepare the data for analysis, ensuring accuracy and usability.

## Import & Clean Data
I begin my importing the necessary libraries and loading the dataset, followed by some data cleaning tasks to ensure data quality. 


```python
# Importing Libraries
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt

#Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

#Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```

## Filter Canada Jobs
Since I am interested in job postings in the Canadian job market, I apply filters to the dataset where I only select roles in Canada.

```python
df_Canada = df[df['job_country'] == 'Canada']
```
# The Analysis
Each Jupyter notebook for this project was targeted at investigating a different aspects of the data job market. Below is how I approached each question:

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the most demanded skills for the top 3 most popular data roles, I filtered out these positions by which ones appeared most popular, and got the top 5 skills for each role. This query highlights the most popular job titles and their top skills, showing which skills I should pay attention to depending on the role I am targeting. 

The code with detailed steps is featured here: [2_Skills_Count](2_Skills_Count.ipynb)

```python
fig, ax = plt.subplots(len(job_titles),1)

sns.set_theme(style='ticks')

for i, job_title in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head(5)
    #df_plot.plot(kind='barh',x='job_skills',y='skill_percent',ax=ax[i],title=job_title)
    sns.barplot(data=df_plot,x='skill_percent',y='job_skills',ax=ax[i],hue='skill_count',palette='dark:b_r')
    ax[i].set_title(job_title)
    ax[i].set_ylabel('')
    ax[i].set_xlabel('')
    ax[i].get_legend().remove()
    ax[i].set_xlim(0,78)

    for n, v in enumerate(df_plot['skill_percent']):
        ax[i].text(v+1,n,f'{v:.0f}%',va='center') 
    if i != len(job_titles)-1:    
        ax[i].set_xticks([])

fig.suptitle('Likelihood of Skills Requested in Canada Job Postings',fontsize=15)
fig.tight_layout(h_pad=0.5)
plt.show()
```
## Results
![Likelihood of Skills Requested in Canada Job Postings](Likelihood%20of%20Skills%20Requested%20in%20Canada%20Job%20Postings.png)

*Bar Graph visualizing the salary for the top 3 data roles and their top 5 skills associated with each*

## Insights
