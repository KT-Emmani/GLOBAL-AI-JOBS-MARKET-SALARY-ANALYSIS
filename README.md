# GLOBAL-AI-JOBS-MARKET-&-SALARY-ANALYSIS



#### Certainly! Let me share with you the exciting project I recently completed. 📊🔍



## Table of Content
- [Overview](#overview)
- [Dataset](#dataset)
- [Data Cleaning and Preparation](#data-cleaning-and-preparation)
- [Data Analysis](#data-analysis)
- [Dashboard](#dashboard)

  


## Overview 
This project aims to conduct comprehensive analysis and visualizes AI jobs market trends and salary globally from January 2024 to March 2025. It focuses on key performance indicators (KPIs) such as total Job posting, total annual salary, avaerage salary, number of AI jobs, avaerage job posting duration with detailed breakdowns by monthly postings, experience level, company size, job positions, remote types, year of experience, skills, country, industry, company, educational level, employment type, benefit score and employee residence (employers target market). The dashboard is designed to help stakeholders understand the factors that influence AI jobs salaries, job patterns based on country, industry and company.

## Dataset

- ###  Source
This dataset is a csv file and can be retrieved from Kaggle.com [download](https://www.kaggle.com/datasets/bismasajjad/global-ai-job-market-and-salary-trends-2025?select=ai_job_dataset.csv). 

- ### Dataset Description
  The dataset consist of the following columns;


  - job_id - Unique identifier for each job posting
  - job_title - Standardized job title
  - salary_usd - Annual salary in USD
  - salary_currency - Original salary currency
  - salary_local - Salary in local currency	
  - experience_level- EN (Entry), MI (Mid), SE (Senior), EX (Executive)	
  - employment_type	- FT (Full-time), PT (Part-time), CT (Contract), FL (Freelance)
  - company_location - Country where company is located	
  - company_size - S (Small <50), M (Medium 50-250), L (Large >250)
  - employee_residence - Country where employee resides	
  - remote_ratio - 0 (No remote), 50 (Hybrid), 100 (Fully remote)	
  - required_skills -	Top 5 required skills (comma-separated) 
  - education_required	- Minimum education requirement	
  - years_experience	- Required years of experience
  - industry	- Industry sector of the company
  - posting_date - Date when job was posted	Date
  - application_deadline	- Application deadline	
  - job_description_length	- Character count of job description
  - benefits_score	- Numerical score of benefits package (1-10)
  - Company Name

- ### Dataset Sample
  
   <img width="3000" height="1904" alt="ai_job_dataset - Excel 7_17_2025 5_19_31 PM" src="https://github.com/user-attachments/assets/3b875da6-816b-4e1f-9810-13ee92919129" />



## Data Cleaning and Preparation

- ### SQL
   - Launched Microsoft SSMS database, connected to the SQL Server.
   - Created a database to import the data as a table for the analysis.
   - Used the Window function to calculate the Average Salary over Job_Title, Experience_level, Employment_Type, Company_Location. This was done to know the average salary of a specific job title using the PARTITION BY Clause. The average salary is then repeated for all rows in the respective job title, experience level, employment type and company's location.
   - Used the CASE Statement to replace data in some fields when it meet a certain conditions. These fields are experience_level, employment_type, remote_ratio and company_size. Also, to group the benefit_score.
   - Used CTE to create a temporary table for complex queries and referencing.
   - Created a Table View for my final data for the analysis and visualization.

``` sql
CREATE VIEW Tech_Jobs_Market AS
-- Based Query--
WITH Job_Market AS
(
SELECT 
	Job_id,
    Job_title,
    salary_usd,
	Salary_usd/12 AS Monthly_Salary,
	AVG(Salary_usd) OVER (PARTITION BY Job_Title, Experience_level, Employment_Type, Company_Location ORDER BY  Job_Title, Experience_Level, Employment_Type, Company_Location) AS AVG_Salary,
    experience_level,
	-- Rename the experience level column--
		CASE 	
			WHEN experience_level = 'EN' THEN 'Entry '
            WHEN experience_level = 'MI' THEN 'Middle'
            WHEN experience_level = 'SE' THEN 'Senior'
            ELSE 'Executive'
		END AS Exp_Lvl,
    employment_type,
	-- Rename the employment level column--
		CASE 	
			WHEN employment_type = 'CT' THEN 'Contract'
            WHEN employment_type = 'FT' THEN 'Full Time'
            WHEN employment_type = 'PT' THEN 'Part Time'
            ELSE 'Freelance'
		END AS Empl_Type,
    company_location,
    company_size,
	-- Rename the company size column--
		CASE 
			WHEN company_size = 'S' THEN 'Small < 50'
            WHEN company_size = 'M' THEN 'Medium 50 - 250'
            ELSE 'Large >250'
		END AS Comp_Size,
    employee_residence,
    remote_ratio,
		CASE
			WHEN remote_ratio = '100' THEN 'Fully Remote'
            WHEN remote_ratio = '50' then 'Hybrid'
            ELSE 'No Remote'
		END AS Remote_Type, 
    required_skills,
    education_required,
    years_experience,
    industry,
    posting_date,
	DATEDIFF(Day, Posting_date, application_deadline) AS Recruitment_Period,
    benefits_score,
		CASE 
			WHEN benefits_score <= 5.9 THEN '5.0 - 5.9'
			WHEN benefits_score BETWEEN 6 AND 6.9 THEN '6.0 - 6.9'
			WHEN benefits_score BETWEEN 7 AND 7.9 THEN '7.0 - 7.9'
			WHEN benefits_score BETWEEN 8 AND 8.9 THEN '8.0 - 8.9'
			WHEN benefits_score BETWEEN 9 AND 9.9 THEN '9.0 - 9.9'
			ELSE '10.0' 
		END AS Benefit_score_Bucket,
    company_name
FROM ai_job_dataset
)
-- Final Data--
SELECT 
	job_id,
    Job_title,
    salary_usd,
	Monthly_Salary,
	AVG_Salary,
	Exp_Lvl,
	Empl_Type,
    company_location,
	Comp_Size,
    employee_residence,
	Remote_Type, 
    required_skills,
    education_required,
    years_experience,
    industry,
    posting_date,
	Recruitment_Period,
    benefits_score,
	Benefit_score_Bucket,
    company_name
FROM Job_Market
```

- ### Power Bi
   - Connected to the SQL Server to import the final data into Power Bi.
   - Created a date table for the time series analysis on the dataset.
   - Used DAX to calculate some measures such as Total Job Postings, Total Annual Salary, Average Salary, Average posting duration.
   - Created a wireframe background using Power Point and uploaded to Power Bi for 
   - Built an interactive dashboard where you can navigate other pages such as Overview, Country, Industry and Company for insights.

 - ### Challenges & Solutions
Analysing the required_skills field was a challenge since all skills required for a unique job posting was grouped together. I resolved it by spliting the required_skills, duplicated the table in Power Query and deleted the other columns excluding the job_id and the skills columns. I then unpivoted all the 5 skills columns against their unique job_id. Then later linked the job_id in skills table to the fact table in the model view.

<img width="3000" height="1904" alt="Power BI Desktop 7_18_2025 6_34_15 PM" src="https://github.com/user-attachments/assets/65dc1bc6-6d0b-42db-a8f7-bee6e769dfb6" />

## Data Analysis

 ### KPI 
 - Over 1.73b annual salaries been offered in the AI Jobs from Jan 2024 to Mar 2025
 - Average Salaries is 115.35k 
 - Average Duration for Job Posting - 44 days
 - No. of AI Jobs - 20
   
 ### Country
 - Most Hiring Country - Germany with 814 jobs offered
 - Least Hirng Country - Norway with 721 jobs Offered
 - Most Paying Country - Switzerland with 170.64k avaerage salary
 - Least Paying Country - India with 84.24k average salary

 ### Industry
 - Most Hiring Industry - Retail with 1,063 jobs offered
 - Least Hiring Industry - Education with 956 jobs offered
 - Most paying Industry - Consulting with 117.60k average salary
 - Least paying Industry - Gaming with 112.98k average salary

  ### Company
 - Most Paying company - TechCorp Inc with 121.21K offered
 - Least Paying Comapny - AI Innovations with 111.51k offered
 - Most Hiring Company - TechCorp Inc with 980 jobs offered
 - Least Hiring Company - Algorithmic Solutions with 885 jobs offered

  ### Other Insights
 - Experience Level influence on Salary - Executive level with avg salary of 187.72k annually (Snr - 122.19k, Middle - 87.96K & Entry - 63.13k)
 - Company Size with the most Hiring = Small - 5,007, Medium - 4995, Large - 4,998
 - Most Remote AI Jobs - No Remote - 33.83%, Hybrid - 33.37%, Fully Remote - 32.80%
 - Most Paying Remote Type - Fully Remote - 116.16K, Hybrid - 115.78K, No Remote - 114.14K
 - Experience Influence on Salary- Positive related, The higher your experience the higher your salary
 - Top 5 Paying Roles
	- Al Specialist - 120.57k
	- Machine Learning Engineer -118.83k
	- Head of AI - 118.54K
	- AI Research Scientist - 117.90k
	- AI Architect - 17.44k

 - Top 5 Hiring Roles
	- Machine Learning Engineering - 772
	- AI Architect - 771
	- Head of AI - 765
	- AI Research Scientist - 756
	- AI Specialist - 728


 - Top 5 Hiring Skiils needed
   	 - Python - 4450
   	 - SQL - 3407
   	 - Tensorflow - 3022
   	 -  Kubernetes - 3009
   	 - Scala - 2794
   
 - Benefit Offered -  9.0-9.99 with 3052 and 10 with 142 least offered benefit
 - Employment type - Full Time with 116.34K, Contract with 115.92k, Freelance with 114.97k, Part Time with 114.15k average salary
 - Top 5 Employee residence -
   	- Sweden - 790
	- France - 781
   	- Denmark - 777
   	- Austria - 776
   	- India - 772

## Dashboard
  <img width="2500" height="1415" alt="Overview" src="https://github.com/user-attachments/assets/f6b8ad2d-07bf-4b7b-bc8e-354c328d91b0" />







<img width="2616" height="1483" alt="Country " src="https://github.com/user-attachments/assets/b32e50fd-963c-4ff1-9663-97d2feaaf22e" />







<img width="2636" height="1498" alt="Industry" src="https://github.com/user-attachments/assets/a64c5712-c2f5-4963-bbb9-ab34ef9914dc" />








<img width="2653" height="1484" alt="Company " src="https://github.com/user-attachments/assets/8a4780f4-bf79-4268-8c6c-39f0bbb69ccc" />




