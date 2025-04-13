## Data Cleaning
- After importing the data into the required database, the first step was to examine the data structure. Before proceeding with cleaning, an initial check revealed the presence of NULL values.

*SQL Statement*  
```
Describe layoffs;
```


*Result*  

![Image](https://github.com/user-attachments/assets/3e21529c-af18-4caa-93c7-86f6bd5c7c45)

- This table represents a dataset tracking layoffs in the different industries, containing key details in various formats. Most columns are text, including company, industry, stage, and country, while numerical data includes total_laid_off and percentage_laid_off. It also records the layoff event date. 

- Since every column contains NULL values, handling missing data will be a crucial part of the data cleaning process.

### 1. Remove Duplicates
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



### 2. Standardise Data

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

### 3. Handle Null or blank values

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


### 4. Remove unnecessary columns

**1). Drop row_num column**

- The row_num column was dropped from the table as it is no longer required for further analysis or processing. This helps streamline the dataset by removing unnecessary columns.

*SQL Statement* 
```
ALTER TABLE layoffs_staging2
DROP COLUMN row_num;
```
*Result*

![Image](https://github.com/user-attachments/assets/eb8fe1cf-8827-4aa6-bc62-a33038dd4409)
