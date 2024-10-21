# Baby Names Trend Analysis

---

### Project Overview

The goal of this project is to explore trends, patterns, and insights in baby name popularity over time. By analyzing this dataset, we aim to uncover the following:

- How baby name popularity has evolved.
- Gender differences in name popularity.
- Predictions for future name trends.

---

### Data Source
This dataset contains baby names by sex for live births in California, based on information from birth certificates. It includes data from 3,153 rows, with fields for year, sex, rank, name, count, and the date of data revision. The dataset covers names ranked by occurrence, providing insight into popular baby names by year and sex
  
---

### Tools
Google BigQuery: The dataset is imported into Google BigQuery, and SQL is used for data exploration, cleaning, aggregation, and analysis.
[See Here](https://data.ca.gov/dataset/most-popular-baby-names)

---

## Data Cleaning and preparation

#### 1. Remove Duplicates
```sql
# Ensure there are no duplicate rows (i.e., rows with the same Year, Sex, Name, and Count).
SELECT DISTINCT Year, Sex, Rank, Name, Count, Data_Revision_Date
FROM `my-project-438016.babyname.names`
```
<img width="1550" alt="Screenshot 2024-10-18 at 09 13 01" src="https://github.com/user-attachments/assets/8be2100e-5b3b-4b0d-ae5a-f4889c40ee7e">

As we can see there isn't any duplicate.

---

#### 2. Find missing values
```sql
# Check for any missing values in the columns.

SELECT *
FROM `my-project-438016.babyname.names`
WHERE Year IS NULL 
   OR Sex IS NULL 
   OR Rank IS NULL 
   OR Name IS NULL 
   OR Count IS NULL 
   OR Data_Revision_Date IS NULL;
```
There isn't any duplicate.

---
#### 3. Find invalid entries
```sql
# Ensure that the Sex column contains only valid entries ('Female' or 'Male').

SELECT *
FROM `my-project-438016.babyname.names`
WHERE Sex NOT IN ('Female', 'Male');
```
there is no duplicate.

---

#### 4. Check Rank and Count Values
```
sql
# Ensure that Rank is positive and within a reasonable range (e.g., 1 to the total number of names).
# Ensure that Count is non-negative.

SELECT *
FROM `my-project-438016.babyname.names`
WHERE Rank <= 0 
   OR Count < 0;
```
There is no data to display.

---

#### 5. Standardise Name Format
```sql
# Ensure that all names are consistently formatted (e.g., first letter capitalized).
SELECT Year, 
       Sex, 
       Rank, 
       INITCAP(Name) AS Name, 
       Count, 
       Data_Revision_Date
FROM `my-project-438016.babyname.names`
```

---

#### 6. Trim Whitespace
```
sql
# Remove leading and trailing spaces from the Name column to avoid discrepancies.

SELECT Year, 
       Sex, 
       Rank, 
       TRIM(Name) AS Name, 
       Count, 
       Data_Revision_Date
FROM `my-project-438016.babyname.names`
```

---

### Data Analysis

#### 1. Most Popular Names (Overall & By Year)
I want to identify the most popular names in the dataset both overall and by year. This will give me a sense of which names are the most frequently used and how trends in naming change over time.
```sql
SELECT Year, Name, SUM(Count) AS Total_Count
FROM my-project-438016.babyname.names
WHERE Sex='Female' OR Sex='Male'
GROUP BY Year, Name
ORDER BY Total_Count DESC
LIMIT 10;
```

<img width="511" alt="Screenshot 2024-10-21 at 16 15 32" src="https://github.com/user-attachments/assets/b7edec80-d207-462c-9673-5b10c73a5d9c">

The results show that Michael was the top name in 1961 and remained popular in the following years, with a peak of 9444 occurrences in 1961. While the count gradually declined over the years, the name stayed consistently popular.

---

#### 2. 

