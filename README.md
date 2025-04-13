
## Analysis of Layoff Trends: Data Cleaning and Exploratory Data Analysis (EDA) Using MySQL

## Executive Summary
This project analyses global layoffs from March 2020 to March 2023, with a primary focus on the impact of the COVID-19 pandemic across various industries. The dataset, encompassing over 1,800 companies, provides crucial insights into the number of employees laid off, the percentage affected, the companies' stages of development, and their financial status.

Data cleaning involved handling NULL values, removing duplicates, standardising industry names, and correcting discrepancies in location and company names. Following this, exploratory data analysis (EDA) identified key trends, such as the significant number of layoffs in the Consumer, Retail, and Finance sectors, with the United States being the largest contributor to layoffs.

Large companies, such as Amazon, Google, and Meta, dominated the layoff statistics, often due to restructuring efforts. Post-IPO companies and later-stage companies also experienced substantial workforce reductions.

March 2023 marked the highest number of layoffs in the entire period, indicating a potential shift in the economy driven by inflation and market adjustments. This project provides valuable insights into the economic disruptions caused by the pandemic and offers a data-driven perspective on workforce trends globally.

## Table of Content

[1. Introduction](#1-introduction)  
[2. Exploratory Data Analysis (EDA) and Insights](#2-exploratory-data-analysis-eda-and-insights)  
[3. Conclusion](#3-conclusion)  
[4. Future Improvements](#4-future-improvements)  
## 1. Introduction
### 1.1. Overview

Layoffs refer to the dismissal of employees from their jobs due to various factors, often driven by a company's financial situation, strategic changes, or economic challenges. In recent years, layoffs have been heavily influenced by the global disruption caused by the COVID-19 pandemic, which resulted in widespread economic uncertainty and operational adjustments across industries. The pandemic forced businesses to adapt rapidly to new conditions, and as a result, many companies had to downsize their workforce to cope with financial pressures, changes in demand, or shifts in business models.

This dataset provides a comprehensive view of global company layoffs that occurred between March 2020 and March 2023, primarily influenced by the COVID-19 pandemic. The dataset contains key information about companies in various industries, including the number of employees laid off, the percentage of layoffs, the stage of the company, and the funds raised by each company. The data covers companies in multiple countries, including the United States, Australia, India, Brazil, and others, allowing for a global perspective on the layoffs trend. It helps analyse trends in post-COVID-19 layoffs and their impact on the global industry. It tracks layoffs from various companies, capturing key details such as company name, location, industry, number of employees laid off, percentage of workforce affected, date of layoffs, company stage, country, and funds raised.

### 1.2. Project Objective

The objective of this project is to analyse the global trend of layoffs from March 2020 to March 2023, focusing on the impact of the COVID-19 pandemic on businesses across various industries and countries. 

Specific objectives include:
- Clean and preprocess a dataset containing company layoffs using MySQL.

- Remove duplicates, standardise data formats, and handle missing values.

- Perform exploratory data analysis (EDA) to uncover trends in layoffs across industries and time periods.

- Generate insights that can help in understanding layoff patterns and their impact.

### 1.3. Data Description
The dataset includes the following columns:


| Column Name          | Data Type  | Description                                |
|----------------------|-----------|--------------------------------------------|
| company             | VARCHAR   | The company name                           |
| location             | VARCHAR   | The location of the company (city or region)                           |
| industry           | VARCHAR   | The industry sector                        |
| total_laid_off      | INTEGER       | Number of employees laid off               |
| percentage_laid_off | FLOAT     | Percentage of workforce affected           |
| date                | DATE      | The date of the layoff event               |
| stage              | VARCHAR   | Company stage (e.g., Series A, Post-IPO, etc.) |
| country            | VARCHAR   | Country where layoffs occurred             |
| funds_raised_millions            | INTEGER   | The amount of funds raised by the company in millions             |



## 2. Exploratory Data Analysis (EDA) and Insights
### 2.1. Layoffs Overview

**2.1.1. Key Observations: Layoffs Overview**

- **How many companies have reported layoffs and the total number of layoffs?**

*SQL Statement* 
```
SELECT COUNT(DISTINCT company) AS Total_Company, SUM(total_laid_off) AS Total_Laid_Off
FROM layoffs_staging2;
```

*Result*

There are 1,885 companies that have reported layoffs, and the total number of layoffs across these companies is 413,219 people.

![Image](https://github.com/user-attachments/assets/694f9cd7-62b3-43b6-9f45-442d26d5bedc)

- **What is the highest number of total layoffs and the highest percentage of layoffs?**

*SQL Statement* 
```
SELECT max(total_laid_off) AS Max_Laid_Off, max(percentage_laid_off) AS Max_Percent_Laid_off
FROM layoffs_staging2;
```

*Result*

The highest number of layoffs reported at one time is 12,000 people, which also represents the highest percentage of layoffs, at 1%.

![Image](https://github.com/user-attachments/assets/ec56bbde-f6f6-45c1-8c9d-893e8b531029)

- **Drilling down to see which company has the highest percentage**

*SQL Statement* 
```
SELECT * 
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY total_laid_off DESC;
```

*Result*

Many companies have laid off 1% of their workforce, with Katerra having the highest number, totaling 2,434 employees.

![Image](https://github.com/user-attachments/assets/9af26aa4-114f-4e12-a8f5-a6b23282950c)

**2.1.2. Insights: Layoffs Overview**

- 1885 companies laid off 413,219 employees across various industries from March 2020-2023, indicating widespread layoffs globally.

- The maximum layoffs in a single company is 12,000, while 1% is the highest layoff rate, showing that even large layoffs often affect a small percentage of the workforce.

- Many companies have reported layoffs at a rate of 1% each time, but the total number of people affected varies. The highest number of layoffs at 1% occurred at Katerra, with 2,434 people laid off on June 1st, 2021.

### 2.2. Layoffs Trends in General

**2.2.1. Key Observations: Layoffs Trends in General**

- **Which industry had the highest number of total layoffs?**

*SQL Statement* 
```
SELECT industry, COUNT(DISTINCT company), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY industry
ORDER BY 3 DESC;
```

*Result*

The 'Consumer' industry has the highest number of layoffs, with a total of 46,422 people laid off, followed closely by the 'Retail' industry, with 46,093 people laid off, showing a small difference between the two.

![Image](https://github.com/user-attachments/assets/bfeb830d-7d67-4fba-a217-d7b4a6146805)

![Image](https://github.com/user-attachments/assets/ac769391-95cc-4e38-9ac8-eff2baca86b4)

- **Which country had the highest number of total layoffs?**

*SQL Statement* 
```
SELECT country, sum(total_laid_off) 
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;
```

*Result*

The United States had the highest number of total layoffs, with 277,199 people affected, which is significantly higher compared to other countries, where the total layoffs of each country are all less than 37,000 people.

![Image](https://github.com/user-attachments/assets/ca59c701-7b33-4574-a3df-b15f03c45feb)

![Image](https://github.com/user-attachments/assets/eff90037-1d68-4a54-bfa5-50da1b0f1e7d)

![Image](https://github.com/user-attachments/assets/ba023138-f5bc-499c-8a35-d4f96df37642)


- **Which company had the highest number of total layoffs?**

*SQL Statement* 
```
SELECT company, sum(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```
*Result*

Amazon has the highest number of total layoffs in a single instance, with 18,150 people affected.

![Image](https://github.com/user-attachments/assets/031c476c-0d8a-4c19-92ff-9d6c8d800b13)

- **Which company has reported layoffs more than once?**

*SQL Statement* 
```
SELECT company, count(company)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 desc;
```
*Result*

A large number of companies reported layoffs more than once, with Loft and WeWork having the highest frequency, each reporting layoffs 6 times.

![Image](https://github.com/user-attachments/assets/d6c16815-21dc-415f-a1cb-cf3132425b29)

- **The total layoffs per stage (If the stage with the highest layoffs also has the most companies)**

*SQL Statement* 
```
SELECT stage, COUNT(DISTINCT company), SUM(total_laid_off) 
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;
```
*Result*

The layoffs were heavily concentrated in Post-IPO companies, with 209K people affected, indicating that public market conditions had a major impact.

![Image](https://github.com/user-attachments/assets/e42a34f6-f097-4371-a291-25d12bb63cd3)

**2.2.2. Insights: Layoffs Trends in General**

- Layoffs occur across all company stages, even companies at later stages (Post-IPO, Acquired) are not immune to workforce reductions during the COVID pandamic.

- The Consumer industry leads with the highest total layoffs, affecting 46K employees across 94 companies, followed by Retail (46K layoffs in 154 companies) and Finance (32K layoffs across 242 companies), reflecting a significant concentration of layoffs in these sectors.

- Industries like Manufacturing (60 layoffs in 2 companies), Fin-Tech, and Aerospace were the least affected by layoffs, indicating fewer job cuts in these areas.

- The United States leads by a significant margin, with 277K layoffs, marking it as the largest contributor to global layoffs. Other countries, such as India, the Netherlands, Sweden, Brazil, and Germany, also report considerable layoffs, but their numbers are far lower than that of the United States, suggesting a global imbalance in layoff distribution.

- The larger companies tend to have the most layoffs, likely reflecting their larger workforce and the widespread impact of restructuring or downsizing efforts, especially in industries like consumer, retail, and transportation.

- Amazon and Google lead the layoff statistics, with 18K and 12K layoffs, respectively. Other significant contributors include Meta, Salesforce, Microsoft, and other giants like Ericsson, Uber, Dell, and Booking.com. These figures suggest that large-scale companies are facing substantial workforce reductions, potentially due to market adjustments or restructuring.

- Post-IPO companies, which have transitioned from private to public ownership, experienced a massive total of 208,852 layoffs across 311 companies. This large number likely reflects significant challenges post-IPO, such as restructuring efforts, cost-cutting measures, or market fluctuations that forced companies to scale back their workforce.

- Companies in the Seed and Series A stages, as well as acquired companies, had comparatively lower layoff totals. This may suggest that smaller, early-stage companies or those acquired by larger entities tend to face less workforce reduction, perhaps due to their more controlled growth or the integration process that focuses on preserving talent post-acquisition.

### 2.3. Layoffs Trends over Time
**2.3.1. Key Observations: Layoffs Trends over Time**

- **What is the date range for massive layoffs?**

*SQL Statement* 
```
SELECT min(`date`), max(`date`) 
FROM layoffs_staging2;
```
*Result*

This dataset contains values from March 2020 to March 2023, a period significantly affected by the COVID-19 pandemic. During this time, many companies experienced layoffs as a result of economic disruptions and workforce reductions due to the pandemic.

![Image](https://github.com/user-attachments/assets/5524b42b-c787-4eaa-a3ee-9e22da193eba)

- **Which year had the highest total number of layoffs?**

*SQL Statement* 
```
SELECT YEAR(`date`) AS Year, sum(total_laid_off) As Total_layoffs
FROM layoffs_staging2
GROUP BY YEAR(`date`)
ORDER BY 2 desc;
```
*Result*

The highest number of layoffs peaked in 2022 at approximately 177K, followed by 132K in 2023 and 87K in 2020. The lowest was in 2021, with only 16K, marking the early stages of layoff trends influenced by the pandemic.

![Image](https://github.com/user-attachments/assets/790ab206-4fc9-4c23-b456-7e81db2027f2)

- **Total layoffs each year by month**

*SQL Statement* 
```
SELECT substring(`date`,1,7) AS `MONTH`, sum(total_laid_off)
FROM layoffs_staging2
WHERE substring(`date`,1,7) IS NOT NULL
GROUP BY `MONTH`
ORDER BY 1;
```
*Result*

Total layoffs from March - December 2020.

![Image](https://github.com/user-attachments/assets/c3bef96d-d955-4d61-a427-a9c6d108ac22)

Total layoffs from January - December 2021.

![Image](https://github.com/user-attachments/assets/9af9c011-7197-45de-9848-cd7d180dc1cb)

Total layoffs from January 2022 - December 2021, and January - March 2023.

![Image](https://github.com/user-attachments/assets/57dd66a7-c91e-476e-904e-b701021e99f9)

- **Rolling sum of layoffs based on month**

*SQL Statement* 
```
WITH rolling_total AS
(
SELECT substring(`date`,1,7) AS `MONTH`, sum(total_laid_off) AS total_off
FROM layoffs_staging2
WHERE substring(`date`,1,7) IS NOT NULL
GROUP BY `MONTH`
ORDER BY 1
)
SELECT `MONTH`, total_off,
sum(total_off) OVER(ORDER BY `MONTH`) AS Rolling_Sum
FROM rolling_total;
```

*Result*

The dataset reflects the evolving impact of the COVID-19 pandemic on layoffs, with a significant surge in 2020, a peak in November 2022 with over 57,000 layoffs, and a continued impact into early 2023, reaching over 413K cumulative layoffs by March 2023.

![Image](https://github.com/user-attachments/assets/68ef55ec-5bb1-43e6-920e-a52987013d74)

![Image](https://github.com/user-attachments/assets/016fdf37-a882-4f63-8fcb-93a5a4af7fce)

- **Select company by the year people get laid off**

*SQL Statement* 
```
SELECT company, YEAR(`date`), sum(total_laid_off) 
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
ORDER BY 3 DESC;
```
*Result*

The companies with the largest single layoff events each year are well-known and major players, such as Google, which had the highest number of layoffs in 2023 with 12K employees affected in one go. This was followed by META with 11K layoffs in 2022 and Amazon with 10K in the same year.

![Image](https://github.com/user-attachments/assets/ff5e25c3-1c39-45df-8a25-92b53b3a1ebd)

- **Which company had the highest number of layoffs in a particular year?**

*SQL Statement* 
```
WITH company_year (company, years, total_laid_off) AS
(
SELECT company, YEAR(`date`), sum(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
), Company_Year_RANK AS
(SELECT *, DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) AS Ranking
FROM company_year
WHERE years IS NOT NULL
)
SELECT *
FROM Company_Year_RANK
WHERE Ranking <= 5
;
```
*Result*

The highest number of layoffs in March - December 2020.

![Image](https://github.com/user-attachments/assets/35bec2f0-414d-442d-b2fc-f44933af2e14)

The highest number of layoffs in 2021.

![Image](https://github.com/user-attachments/assets/a9daa91b-b1c1-4459-add6-5be4c3eaa480)

The highest number of layoffs in 2022.

![Image](https://github.com/user-attachments/assets/b5bd8fc1-285a-423c-adae-180dae57c4b3)

The highest number of layoffs in January - March 2023.

![Image](https://github.com/user-attachments/assets/ac5800c7-777d-40d7-a90b-6aaa4fbef389)


**2.3.2. Insights: Layoffs Trends over Time**

- March and April 2020 experienced massive layoffs (11K and 29K, respectively), likely due to the sudden global economic disruptions from COVID-19. This was followed by high figures in May, indicating the immediate impact of lockdowns and uncertainty across industries.

- After mid-2020, layoffs dropped significantly, with months like September and October seeing much smaller numbers (below 1,000). This suggests that businesses were adjusting to the pandemic's new normal and possibly re-opening or restructuring operations.

- Layoffs spiked in 2022, particularly from May to November, due to pandemic aftereffects, inflation, and global economic shifts. November saw morethan 57K layoffs, signaling major restructuring. This trend continued into early 2023 with a high of 89K layoffs in January before decreasing by March.

- Some companies, such as Uber (2020) and Booking.com (2020), experienced significant layoffs during the earlier years of the pandemic, possibly as a result of reduced demand for travel, transportation, and other services affected by lockdowns.

- The year 2023 saw the highest number of layoffs (132,357), despite it being only three months from January to March, indicating a significant increase compared to previous years. This suggests major workforce reductions, possibly due to economic downturns, cost-cutting measures, or industry shifts.

- The second-highest layoffs occurred in 2020 (87,358), which aligns with the early COVID-19 pandemic. Many businesses faced disruptions, leading to job losses, especially in industries like travel, hospitality, and retail.

- Layoffs dropped significantly in 2021 (16,343), suggesting a period of recovery as businesses adapted to post-pandemic conditions, government support measures took effect, or hiring resumed.

- The massive increase in layoffs from 2022 (17,761) to 2023 (132,357) could indicate new challenges, such as global economic uncertainty, inflation, interest rate hikes, or shifts in technology and AI automation affecting jobs.

- Company Ranking Trends:

    -  In 2020, Uber and Booking.com top the list, with companies in travel and hospitality leading.
    - In 2021, Bytedance and Katerra were at the top, showing that tech companies dominated layoffs in the post-COVID adjustment phase.
    - 2022 saw some of the largest layoffs, with Meta and Amazon leading, signaling ongoing industry-wide restructuring.
    - 2023 follows a similar trend, with major layoffs at Google, Microsoft, and Amazon, indicating continued industry adjustments.

- Despite recovery in many sectors, big companies, in particular, appear to have continued restructuring their operations into 2023, signaling a possible shift in the market's demand and the overall economy.

## 3. Conclusion
The analysis of global layoffs from March 2020 to March 2023 highlights the profound and far-reaching effects of the COVID-19 pandemic, followed by economic shifts in subsequent years. While the data shows recovery and adjustments across various industries in 2021, 2022 and 2023 witnessed continued significant layoffs, particularly in large and tech companies.

This trend is likely driven by ongoing restructuring, inflationary pressures, and the rapid development of technology that has led to increased automation. The dataset serves as a valuable tool for understanding the larger economic and societal impacts of workforce reductions, providing insights that can guide future business strategies, policy decisions, and workforce planning.

The findings also emphasize the importance of data-driven approaches to identifying trends in layoffs, helping businesses, employees, and policymakers understand and mitigate the impact of layoffs on the global workforce.

## 4. Future Improvements

- One key area for future improvements in this project would be the inclusion of additional information, such as the total number of employees for each company. This data would allow for a more accurate calculation of the layoff percentage, providing meaningful insights into the extent of layoffs relative to company size. 

- Visualisation enhancements could include more advanced techniques, such as interactive dashboards, heatmaps, and network graphs, to provide clearer insights into global layoffs by region, industry, and company stage. Additionally, incorporating geospatial data to map layoffs across countries and regions would help uncover geographic trends, highlighting areas most affected by workforce reductions and enabling a more detailed understanding of the global impact.

- It would be beneficial to extend the dataset by incorporating additional years, ideally up to 2024. This would allow for a more complete understanding of layoffs over an extended period, especially as industries continue to evolve post-pandemic and as economic conditions change. A longer time span would also offer insights into how companies are adapting to new challenges and whether layoffs are part of long-term restructuring efforts.
