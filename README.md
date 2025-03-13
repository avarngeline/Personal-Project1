
## Analysis of Layoff Trends: Data Cleaning and Exploratory Data Analysis (EDA) Using MySQL

## Executive Summary
This project analyses global layoffs from March 2020 to March 2023, with a primary focus on the impact of the COVID-19 pandemic across various industries. The dataset, encompassing over 1,800 companies, provides crucial insights into the number of employees laid off, the percentage affected, the companies' stages of development, and their financial status.

Data cleaning involved handling NULL values, removing duplicates, standardising industry names, and correcting discrepancies in location and company names. Following this, exploratory data analysis (EDA) identified key trends, such as the significant number of layoffs in the Consumer, Retail, and Finance sectors, with the United States being the largest contributor to layoffs.

Large companies, such as Amazon, Google, and Meta, dominated the layoff statistics, often due to restructuring efforts. Post-IPO companies and later-stage companies also experienced substantial workforce reductions.

March 2023 marked the highest number of layoffs in the entire period, indicating a potential shift in the economy driven by inflation and market adjustments. This project provides valuable insights into the economic disruptions caused by the pandemic and offers a data-driven perspective on workforce trends globally.

## Table of Content

[1. Introduction](#1-introduction)  
[2. Data Cleaning](#2-data-cleaning)  
[3. Exploratory Data Analysis (EDA) and Insights](#3-exploratory-data-analysis-eda-and-insights)  
[4. Conclusion](#4-conclusion)  
[5. Future Improvements](#5-future-improvements)  
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


## 2. Data Cleaning
- After importing the data into the required database, the first step was to examine the data structure. Before proceeding with cleaning, an initial check revealed the presence of NULL values.

*SQL Statement*  
```
Describe layoffs;
```


*Result*  

![Image](https://github.com/user-attachments/assets/3e21529c-af18-4caa-93c7-86f6bd5c7c45)

- This table represents a dataset tracking layoffs in the different industries, containing key details in various formats. Most columns are text, including company, industry, stage, and country, while numerical data includes total_laid_off and percentage_laid_off. It also records the layoff event date. 

- Since every column contains NULL values, handling missing data will be a crucial part of the data cleaning process.

### 2.1. Remove Duplicates
**1). Create staging table to back up raw data**

- A staging table acts as a safeguard, providing backup and a safe environment for cleaning and transforming data without compromising the original dataset.

*SQL Statement*  
``` 
CREATE TABLE layoffs_staging
LIKE layoffs; 

INSERT layoffs_staging
SELECT *
FROM layoffs;
```

*Result* 

![Image](https://github.com/user-attachments/assets/f29a907f-35e7-4632-8aa9-1ffd2871fb52)  


**2). Use ROW_NUMBER() with PARTITION BY to identify duplicates** 

- Using ROW_NUMBER() with PARTITION BY helps identify duplicates by assigning a unique sequential number to each row within a specified group. If ROW_NUMBER() > 1, it indicates a duplicate row within that group. This method allows easy identification of duplicates for further handling.

*SQL Statement* 
``` 
WITH duplicate_cte as
(
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions) AS row_num
FROM layoffs_staging
)
SELECT *
FROM duplicate_cte
WHERE row_num >1
;
``` 
*Result*

![Image](https://github.com/user-attachments/assets/6f446496-1305-40e8-9d46-2f18f8fe5f83)

- Five rows were identified as duplicates (row_num > 1) and will be removed.


*SQL Statement* 
```
CREATE TABLE layoffs_staging2 
LIKE layoffs_staging; 

ALTER TABLE layoffs_staging2 
ADD COLUMN row_num INT;

SELECT *
FROM layoffs_staging2;
```
- A new table, layoffs_staging2, is created, which includes a row_num column. Since there is no unique identifier in the original table, it is not possible to directly update or delete the duplicate rows.

- Insert data including row_num into the new table, layoffs_staging2, then delete rows that row_num > 1.

*SQL Statement* 


```
INSERT INTO layoffs_staging2 
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions) AS row_num
FROM layoffs_staging;

DELETE
FROM layoffs_staging2
where row_num > 1;

SELECT *
FROM layoffs_staging2
WHERE row_num > 1;
```
*Result*

Only rows with row_num = 1 remain, indicating that all duplicate rows have been removed.
![Image](https://github.com/user-attachments/assets/23c03378-0f5c-45fd-b095-f5a03a46b890)



### 2.2. Standardise Data

**1). Remove white space of company by using TRIM()**
- To remove leading and trailing white spaces from the company column, the TRIM() function is applied, ensuring that the data is clean and properly formatted for analysis.

*SQL Statement* 
```
SELECT company, trim(company)
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET company = trim(company);
```
*Result*

The white spaces are removed from the company names.

![Image](https://github.com/user-attachments/assets/b2f794fa-7605-4a55-8577-8cac6569a84f)

**2). Standardise the industry names related to Crypto/Crypto Currency/CryptoCurrency by updating them to 'Crypto'.**

- Identify industries that begin with the word 'Crypto' to ensure consistency in standardising industry names. This helps in accurate grouping, filtering, and comparison of industries in the analysis.

*SQL Statement* 
```
SELECT DISTINCT industry 
FROM layoffs_staging2
WHERE industry LIKE 'Crypto%';

UPDATE layoffs_staging2 
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';
```
*Result*

![Image](https://github.com/user-attachments/assets/26dd8537-72b1-41ad-88db-cb69c7f9090d)

Replace all instances beginning with 'Crypto%' to 'Crypto'.

![Image](https://github.com/user-attachments/assets/0c9ff8a0-27ec-4b56-9544-98b4ec808bf7)

**3). Update locations that shows discrepency**

- The discrepancy can be found in Dusseldorf, Florianopolis, and Malmo due to inconsistent naming (e.g., special fonts).

*SQL Statement* 
```
SELECT location
FROM layoffs_staging2
WHERE location LIKE 'Dรผs%';

UPDATE layoffs_staging2 
SET location = 'Dusseldorf'
WHERE location LIKE 'Dรผs%';
```
*Result*

![Image](https://github.com/user-attachments/assets/a20dac43-c247-4f6e-81a8-c509c1d9c770)

Dusseldorf is updated.

![Image](https://github.com/user-attachments/assets/da703d58-21b4-419e-9464-dabcc1d2edd0)
- Replace with Florianopolis

*SQL Statement* 
```
SELECT location
FROM layoffs_staging2
WHERE location LIKE 'flori%';

UPDATE layoffs_staging2 
SET location = 'Florianopolis'
WHERE location LIKE 'flori%';
```
*Result*

![Image](https://github.com/user-attachments/assets/533b043d-396c-4731-b805-2eee3489783b)

Florianopolis is updated.

![Image](https://github.com/user-attachments/assets/4a38eb93-819c-47fb-8ee8-3301dcf2f5a1)

- Replace with Malmo

*SQL Statement* 
```
SELECT location
FROM layoffs_staging2
WHERE location LIKE 'Malm%';

UPDATE layoffs_staging2 
SET location = 'Malmo'
WHERE location LIKE 'Malm%';
```
*Result*

![Image](https://github.com/user-attachments/assets/7a4f8882-8a29-4874-a69e-a6d140e27557)

Malmo is updated.

![Image](https://github.com/user-attachments/assets/dc1b091b-f491-4b29-a398-457eb19e3dfb)

**4). Update country United States. to be consistently United States**

- The discrepancies are found in the location column, specifically in the United States.

*SQL Statement* 
```
SELECT DISTINCT country
FROM layoffs_staging2
ORDER BY country;

UPDATE layoffs_staging2 
SET country = 'United States'
WHERE country LIKE 'United States%';
```
*Result*

![Image](https://github.com/user-attachments/assets/940f832a-acff-4264-b8c3-c703e8a2fd31)

There is only one format of the United States.

![Image](https://github.com/user-attachments/assets/fa9a4f6f-3a75-4c54-940a-c0ec46e0ef9a)

**5). Correct data types**

- The 'date' column in the current data is in text format, so it should be changed to a date format accordingly for proper data handling and analysis.

*SQL Statement* 
```
DESCRIBE layoffs_staging2;
```
*Result*

![Image](https://github.com/user-attachments/assets/50805f4e-bd96-4a5e-a9ac-2013e5c35456)

- Change data of date column from text to Date.

*SQL Statement* 
```
SELECT `date`,
STR_TO_DATE(`date`, '%m/%d/%Y')
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE layoffs_staging2
MODIFY COLUMN `date` DATE;
```
- The percentage_laid_off column is currently stored as text, but the data is actually a decimal number, so it should be updated to the FLOAT data type.

*SQL Statement* 
```
ALTER TABLE layoffs_staging2 
MODIFY COLUMN percentage_laid_off FLOAT;
```

*Result*

The date and percentage_laid_off data types are correctly formatted.

![Image](https://github.com/user-attachments/assets/dfbff245-9a78-468a-9f6d-6fc01a928ac5)

### 2.3. Handle Null or blank values

**1). Check the number of NULL values**

- Using CASE WHEN to calculate the percentage of NULL values in each column. If a column has too many NULL values, it might not be useful for analysis.

*SQL Statement* 
```
SELECT
    (COUNT(CASE WHEN company IS NULL THEN 1 END) / COUNT(*) * 100) AS com_null,
    (COUNT(CASE WHEN location IS NULL THEN 1 END) / COUNT(*) * 100) AS loc_null,
    (COUNT(CASE WHEN industry IS NULL THEN 1 END) / COUNT(*) * 100) AS ind_null,
    (COUNT(CASE WHEN total_laid_off IS NULL THEN 1 END) / COUNT(*) * 100) AS total_laid_null,
    (COUNT(CASE WHEN percentage_laid_off IS NULL THEN 1 END) / COUNT(*) * 100) AS pcent_laid_null,
    (COUNT(CASE WHEN `date` IS NULL THEN 1 END) / COUNT(*) * 100) AS date_null,
    (COUNT(CASE WHEN stage IS NULL THEN 1 END) / COUNT(*) * 100) AS stage_null,
    (COUNT(CASE WHEN country IS NULL THEN 1 END) / COUNT(*) * 100) AS ctry_null,
    (COUNT(CASE WHEN funds_raised_millions IS NULL THEN 1 END) / COUNT(*) * 100) AS fund_null
FROM layoffs_staging2;
```
*Result*

Most of the columns contain null values, with the total_laid_off and percentage_laid_off columns having more than 30% null values.

![Image](https://github.com/user-attachments/assets/6c5a5679-a645-4497-9ad3-0cd77073e57b)

**2). Fill in the NULL or BLANK values with data that can be identified or populated based on available information**

- After reviewing the data, some of the null or blank  values in each column can be filled by population or by researching the companies. For example, the null values in the industry column for companies like Airbnb, Carvana, Bally%, and Juul can be populated with the correct data.

*SQL Statement* 
```
SELECT * 
FROM layoffs_staging2
WHERE industry IS NULL
OR industry = '';
```
*Result*

Four companies with null or blank values in the industry column are:

![Image](https://github.com/user-attachments/assets/d2f44048-7d5e-4e65-b6ce-7f6705bc1504)

- Set the industry of Airbnb to'Travel'.

*SQL Statement* 
```
UPDATE layoffs_staging2
SET industry = 'Travel'
WHERE company = 'Airbnb';
```
*Result*

![Image](https://github.com/user-attachments/assets/ea7b57fa-bf9b-4d3f-a854-79ab83a1842c)

Travel is added into the blank value.

![Image](https://github.com/user-attachments/assets/d3c026df-af8e-4d2e-9995-c303b6d21b5a)

- Set the industry of Carvana to'Transportation'.

*SQL Statement* 
```
UPDATE layoffs_staging2
SET industry = 'Transportation'
WHERE company = 'Carvana';
```
*Result*

![Image](https://github.com/user-attachments/assets/f68704ab-74ae-4da2-8a8f-c37025b13efe)

Transportation is added into the blank value.

![Image](https://github.com/user-attachments/assets/acd0e9db-5dde-41b3-a293-e33755f759a9)

- Set the industry of Juul to 'Consumer'.

*SQL Statement* 
```
UPDATE layoffs_staging2
SET industry = 'Consumer'
WHERE company = 'Juul';
```

*Result*

![Image](https://github.com/user-attachments/assets/dff78b7f-e509-4196-8150-507510acfc84)

Consumer is added into the blank value.

![Image](https://github.com/user-attachments/assets/e50276e8-4b67-441d-a8d6-7e19c15e27de)

- For Bally's Interactive, since there were no other entries for the same company, the industry was filled in by searching on the internet as Other because Bally's Interactive is a gambling company (Source: www.ballys.com)

*SQL Statement* 
```
UPDATE layoffs_staging2
SET industry = 'Other'
WHERE company LIKE 'Bally%';
```
*Result*

![Image](https://github.com/user-attachments/assets/ced30a8c-a8aa-4e55-8775-219232f8072c)


- Fill in the NULL or blank values with "Unknown", which is one of the entries, in the stage column for Advata, Gatherly, Relevel, Verily, Zapp.

*SQL Statement* 
```
UPDATE layoffs_staging2 
SET stage = 'Unknown'
WHERE stage IS NULL;
```

*Result*

![Image](https://github.com/user-attachments/assets/e9414194-8aff-48be-8c33-4c99da0faf87)

- Since date is missing for Blackbaud, so it was filled in by searching on the internet as announcement date on 4 Nov 2022 (www.erpglobalinsights.com)

*SQL Statement* 
```
SELECT * 
FROM layoffs_staging2
WHERE `date` IS NULL; 

UPDATE layoffs_staging2
SET `date` = '2022-11-04'
WHERE `date` IS NULL;
```

*Result*

![Image](https://github.com/user-attachments/assets/8aa06349-c457-41ad-a8db-4a277a7b013a)

Null values is filled in the date column.

![Image](https://github.com/user-attachments/assets/bde6bcdb-4607-4af3-ba2d-3da67d7a1f92)

**3). Check data distribution by using AVG(), MIN(), MAX(), Mode, and Median to perform imputation**

- Checking average, minimum, maximum, mode, and median helps to identify outliers, understand the data's central tendency, and guide imputation for missing values by replacing them with appropriate statistics.

*SQL Statement* 
```
SELECT floor(avg(total_laid_off)), min(total_laid_off), max(total_laid_off) -
FROM layoffs_staging2;
```

*Result*

![Image](https://github.com/user-attachments/assets/4d745a12-d257-4c80-8227-5bea75d2dc01)

- Check mode value

*SQL Statement* 
```
SELECT total_laid_off, COUNT(*) AS frequency 
FROM layoffs_staging2
WHERE total_laid_off IS NOT NULL
GROUP BY total_laid_off
ORDER BY frequency DESC
LIMIT 5;

```

*Result*

![Image](https://github.com/user-attachments/assets/1d40c81f-4869-416b-a1e0-2ac37246aed3)

- Check Median value

*SQL Statement* 
```
WITH numbered_rows AS ( 
    SELECT total_laid_off, ROW_NUMBER() OVER (ORDER BY total_laid_off) AS row_num
    FROM layoffs_staging2
)
SELECT total_laid_off AS Median
FROM numbered_rows
WHERE row_num = (
	SELECT FLOOR(COUNT(*) / 2) + 1
    FROM numbered_rows)
; 
```

*Result*

![Image](https://github.com/user-attachments/assets/9b3de6c1-b6d0-4128-a2b2-274c143d38a2)

**4). Perform imputation in total_laid_off using median values (40) to avoid being influenced by outliers**

- Imputing the total_laid_off column with the median value (40) helps avoid being influenced by outliers, as the median is less affected by extreme values compared to the mean, ensuring more accurate and robust data.

*SQL Statement* 
```
UPDATE layoffs_staging2
SET total_laid_off = 40
WHERE total_laid_off IS NULL;

SELECT *
FROM layoffs_staging2;
```

*Result*

There are no null values in the industry column.

![Image](https://github.com/user-attachments/assets/dd1f53f8-e5c8-4ddd-be22-b7bb99a5ad21)


**5). Drop the rows with missing key data**

- Rows where both the total_laid_off and percentage_laid_off columns contain null values were deleted, as these columns hold essential information. Removing these rows ensures that the dataset maintains relevant and complete data for analysis.

*SQL Statement* 
```
DELETE 
FROM layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;

```

*Result*

![Image](https://github.com/user-attachments/assets/e217dcd3-5225-427c-b663-2d47608f29c4)

- However, percentage_laid_off will not be populated due to insufficient data and its lack of meaning, so these will be left for exploration during the EDA phase.


### 2.4. Remove unnecessary columns

**1). Drop row_num column**

- The row_num column was dropped from the table as it is no longer required for further analysis or processing. This helps streamline the dataset by removing unnecessary columns.

*SQL Statement* 
```
ALTER TABLE layoffs_staging2
DROP COLUMN row_num;
```
*Result*

![Image](https://github.com/user-attachments/assets/eb8fe1cf-8827-4aa6-bc62-a33038dd4409)

## 3. Exploratory Data Analysis (EDA) and Insights
### 3.1. Layoffs Overview

**3.1.1. Key Observations: Layoffs Overview**

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

**3.1.2. Insights: Layoffs Overview**

- 1885 companies laid off 413,219 employees across various industries from March 2020-2023, indicating widespread layoffs globally.

- The maximum layoffs in a single company is 12,000, while 1% is the highest layoff rate, showing that even large layoffs often affect a small percentage of the workforce.

- Many companies have reported layoffs at a rate of 1% each time, but the total number of people affected varies. The highest number of layoffs at 1% occurred at Katerra, with 2,434 people laid off on June 1st, 2021.

### 3.2. Layoffs Trends in General

**3.2.1. Key Observations: Layoffs Trends in General**

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

**3.2.2. Insights: Layoffs Trends in General**

- Layoffs occur across all company stages, even companies at later stages (Post-IPO, Acquired) are not immune to workforce reductions during the COVID pandamic.

- The Consumer industry leads with the highest total layoffs, affecting 46K employees across 94 companies, followed by Retail (46K layoffs in 154 companies) and Finance (32K layoffs across 242 companies), reflecting a significant concentration of layoffs in these sectors.

- Industries like Manufacturing (60 layoffs in 2 companies), Fin-Tech, and Aerospace were the least affected by layoffs, indicating fewer job cuts in these areas.

- The United States leads by a significant margin, with 277K layoffs, marking it as the largest contributor to global layoffs. Other countries, such as India, the Netherlands, Sweden, Brazil, and Germany, also report considerable layoffs, but their numbers are far lower than that of the United States, suggesting a global imbalance in layoff distribution.

- The larger companies tend to have the most layoffs, likely reflecting their larger workforce and the widespread impact of restructuring or downsizing efforts, especially in industries like consumer, retail, and transportation.

- Amazon and Google lead the layoff statistics, with 18K and 12K layoffs, respectively. Other significant contributors include Meta, Salesforce, Microsoft, and other giants like Ericsson, Uber, Dell, and Booking.com. These figures suggest that large-scale companies are facing substantial workforce reductions, potentially due to market adjustments or restructuring.

- Post-IPO companies, which have transitioned from private to public ownership, experienced a massive total of 208,852 layoffs across 311 companies. This large number likely reflects significant challenges post-IPO, such as restructuring efforts, cost-cutting measures, or market fluctuations that forced companies to scale back their workforce.

- Companies in the Seed and Series A stages, as well as acquired companies, had comparatively lower layoff totals. This may suggest that smaller, early-stage companies or those acquired by larger entities tend to face less workforce reduction, perhaps due to their more controlled growth or the integration process that focuses on preserving talent post-acquisition.

### 3.3. Layoffs Trends over Time
**3.3.1. Key Observations: Layoffs Trends over Time**

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


**3.3.2. Insights: Layoffs Trends over Time**

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

## 4. Conclusion
The analysis of global layoffs from March 2020 to March 2023 highlights the profound and far-reaching effects of the COVID-19 pandemic, followed by economic shifts in subsequent years. While the data shows recovery and adjustments across various industries in 2021, 2022 and 2023 witnessed continued significant layoffs, particularly in large and tech companies.

This trend is likely driven by ongoing restructuring, inflationary pressures, and the rapid development of technology that has led to increased automation. The dataset serves as a valuable tool for understanding the larger economic and societal impacts of workforce reductions, providing insights that can guide future business strategies, policy decisions, and workforce planning.

The findings also emphasize the importance of data-driven approaches to identifying trends in layoffs, helping businesses, employees, and policymakers understand and mitigate the impact of layoffs on the global workforce.

## 5. Future Improvements

- One key area for future improvements in this project would be the inclusion of additional information, such as the total number of employees for each company. This data would allow for a more accurate calculation of the layoff percentage, providing meaningful insights into the extent of layoffs relative to company size. 

- Visualisation enhancements could include more advanced techniques, such as interactive dashboards, heatmaps, and network graphs, to provide clearer insights into global layoffs by region, industry, and company stage. Additionally, incorporating geospatial data to map layoffs across countries and regions would help uncover geographic trends, highlighting areas most affected by workforce reductions and enabling a more detailed understanding of the global impact.

- It would be beneficial to extend the dataset by incorporating additional years, ideally up to 2024. This would allow for a more complete understanding of layoffs over an extended period, especially as industries continue to evolve post-pandemic and as economic conditions change. A longer time span would also offer insights into how companies are adapting to new challenges and whether layoffs are part of long-term restructuring efforts.
