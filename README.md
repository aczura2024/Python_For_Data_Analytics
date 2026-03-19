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

* I see that SQL is highly valued for data analysts, data engineers and senior data engineers.
* Excel is the second most popular for data analysts, but not listed for data engineers.
* Python is in high demand especially for data engineers (61%), but only 32% for data analysts.
* Data engineers are required to have more specialized skills (such as AWS, Azure, Spark) whereas data analysts are expected to be proficient in more general data management tools (Excel, Tableau). 


## 2. How are in-demand skills trending for Data Analysts?

To find how skills are trending in 2023 for Data Analysts, I filtered data analyst positions and grouped the skills by the month of each job posting. This got me the top 5 skills of data analysts by month, showing how popular skills were throughout 2023.

View the code I used with detailed steps here: [3_Skills_Trend](3_Skills_Trend.ipynb)

### Visualize Data

```python
df_plot = df_DA_Canada_percent.iloc[:,:5] #select the top 5 skills

sns.lineplot(data=df_plot,dashes=False,palette='tab10') #generate the line plot
sns.set_theme(style='ticks')
sns.despine()


#specify settings
plt.title('Trending Top Skills for Data Analysts in Canada')
plt.ylabel('Likelihood in Job Posting')
plt.xlabel('2023')
plt.legend().remove()

from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(decimals=0))


#code to ensure line labels are not close together
offsets = [0.5, 2, 1, -1, 0]   # tweak these as needed

for i in range(5):
    plt.text(
        11.2,
        df_plot.iloc[-1, i] + offsets[i],
        df_plot.columns[i],
        va='center'
    )

```
### Results
![](Trending%20Top%20Skills%20for%20Data%20Analyst%20in%20Canada.png)

Bar graph visualizing the trending top skills for data analysts in Canada in 2023.



### Insights
* We see that demand for SQL remained consistently high throughout the year, staying at over 50% in job postings.
* We wee that the demand for Excel spikes around may, then drops over the rest of the year.
* Python remains steady around 40% throughout the year. 
* Tableau and Power BI tend to be in relatively lower demand. 



## 3. How well do jobs and skills pay for Data Analysts?
To identify the highest-paying roles and skills, I only got jobs in Canada and examined their median salary. First, I looked at the salary distributions of common data jobs like Data Scientist, Data Engineer and Data Analyst, to see which job tends to pay the most. 

View the notebook with detailed code steps here: [4_Salary_Analysis](4_Salary_Analysis.ipynb).

### Visualize Data

```python
sns.boxplot(data=df_Canada_top6,x='salary_year_avg',y='job_title_short',order=job_order)
sns.set_theme(style='ticks')

# this is all the same
plt.title('Salary Distributions of Data Jobs in Canada')
plt.xlabel('Yearly Salary (USD)')
plt.ylabel('')
plt.xlim(0,600000)
ticks_x = plt.FuncFormatter(lambda y, pos: f'${int(y/1000)}K')
plt.gca().xaxis.set_major_formatter(ticks_x)
plt.show()
```


### Results
![](Salary%20Distributions%20of%20Data%20Jobs%20in%20Canada.png)


### Insights
* Machine learning engineers have the highest median salary at almost $150K USD per year.
* Data Analysts have the lowest median salary at just below $100K USD per year.
* Median salaries are fairly equal for Senior Data Engineers, Software Engineers and Data Scientists.

## Highest Paid & Mosted Demanded Skills for Data Analysts
Next, I zoomed in to focus my analysis only on data analyst roles. I investigated highest-paid skills and the most in-demand skills. I used two bar charts to showcase these. 


### Visualize Data

```python

fig, ax = plt.subplots(2, 1)  

# Top 10 Highest Paid Skills for Data Analysts
sns.barplot(data=df_DA_top_pay, x='median', y=df_DA_top_pay.index, hue='median', ax=ax[0], palette='dark:b_r')
ax[0].legend().remove()

# Top 10 Most In-Demand Skills for Data Analysts')
sns.barplot(data=df_DA_skills, x='median', y=df_DA_skills.index, hue='median', ax=ax[1], palette='light:b')
ax[1].legend().remove()
plt.show()
```

### Results
Here's the breakdown of the highest-paid and most in-demand skills for data analysts in the US:
![](Highest%20Paid%20Skills%20for%20Data%20Analysts%20in%20Canada.png)

Two separate bar graphs visualizing the highest paid skills and most in-demand skills for data analysts in the US

### Insights
* Top-paying skills are heavily cloud and big data focused, with tools like Looker, Redshift, Snowflake, and Hadoop leading salaries well above $110K, while traditional programming skills like JavaScript are slightly lower.

* High-demand skills are more general-purpose and widely used, including Snowflake, Spark, Python, SQL, and Excel, clustering around the ~$95K–$120K range.

* There is a gap between demand and pay for some tools—for example, Excel and SQL are highly in demand but not top-paying, whereas specialized tools like Looker and Redshift command higher salaries but are less broadly listed among the most in-demand skills.


## 4. What are the most optimal skills to learn for Data Analysts?
To identify the most optimal skills to learn (the ones that are the highest paid and the highest in demand) I calculated the percent of skill demand and the median salary of these skills. To easily identify which are the most optimal skills to learn.

View my notebook with the detailed code here: [5_Optimal_Skills](5_Optimal_Skills.ipynb)


### Visualize Data
```python
from adjustText import adjust_text

plt.scatter(df_DA_skills_high_demand['skill_percent'], df_DA_skills_high_demand['median_salary'])
plt.show()
```

### Results
![](Most%20Optimal%20Skills%20for%20Data%20Analysts%20in%20Canada.png)
A scatter plot visualizing the most optimal skills (high paying & high demand) for data analysts in Canada.

### Insights

* High‑salary skills like Looker and Snowflake offer strong pay but appear in relatively few job postings.

* Widely demanded skills such as Python and SQL dominate the market yet correspond to more moderate median salaries.

* Cloud and big‑data tools (AWS, GCP, Spark, Hadoop) cluster in the mid‑range for both demand and salary, showing balanced but not standout advantages.

## Visualizing Different Technologies

### Visualize Data

```python
from matplotlib.ticker import PercentFormatter
sns.scatterplot(
    data=df_DA_skills_tech_high_demand,
    x='skill_percent',
    y='median_salary',
    hue='technology'
)
plt.show()
```

### Results
![](Most%20Optimal%20Skills%20for%20Data%20Analysts%20in%20Canada%20Technology.png) 

A scatterplot visualizing the most optimal skills (high paying & high demand) for data analysts in Canada with colour labels for technology. 

### Insights
* High‑salary but low‑prevalence skills (e.g., Looker, Snowflake) sit at the top of the chart, offering strong pay but fewer job postings.

* Core programming and analyst tools like Python, SQL, and Excel dominate job demand but correspond to more moderate median salaries.

* Cloud and big‑data technologies (AWS, GCP, Spark, Hadoop) cluster in the mid‑range, offering balanced demand and compensation without extreme trade‑offs.


# What I Learned

# Insights

# Challenges I Faced
This project presented some challenges with provided some useful learning opportunities:

* **Data Inconsistencies**: Handling missing or inconsistent data entries requires careful consideration and thorough data-cleaning techniques to ensure the integrity of the analysis.
* **Complex Data Visualization**: Designing effective visual representations of complex datasets was challenging but critical for conveying insights clearly and compellingly.
* **Balancing Breadhth and Depth**: Deciding how deeply to dive into each analysis while maintaining a braod overview of the data landscape required constant balancing to ensure wide coverage of topics without getting too focused on details.


# Conclusion
This exploration into the data analyst job market has been incredibly insightful and informative. It has highlighted the critical skills and trends that shape this ever-changing field. The insights I got enhance my understanding and provide actionable guidance for anyone looking to advance their career in data analytics. As the job market continues to change, ongoing analysis will be essential to stay at the top of my game in data analytics. This project provides a solid foundation for future explorations and emphasizes the importance of continuous learning and adaptation in the field of data. 

