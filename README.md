# Baby Names Trend Analysis

---

### Project Overview

The goal of this project is to explore trends, patterns, and insights into baby name popularity over time. By analyzing this dataset, we aim to uncover the following:

- How baby name popularity has evolved.
- Gender differences in name popularity.
- Predictions for future name trends.

---

### Data Source
This dataset contains baby names by sex for live births in California, based on information from birth certificates. It includes data from 3,153 rows, with fields for the year(1960-2022), sex, rank, name, count, and the date of data revision. The dataset covers names ranked by occurrence, providing insight into popular baby names by year and sex
  
---

### Tools
Google BigQuery: The dataset is imported into Google BigQuery, and SQL is used for data exploration, cleaning, aggregation, and analysis.
[See Here](https://data.ca.gov/dataset/most-popular-baby-names)

---

### Data Cleaning and preparation

#### 1. Remove Duplicates
```SQL
# Ensure there are no duplicate rows (i.e., rows with the same Year, Sex, Name, and Count).
SELECT DISTINCT Year, Sex, Rank, Name, Count, Data_Revision_Date
FROM `my-project-438016.babyname.names`
```
<img width="1550" alt="Screenshot 2024-10-18 at 09 13 01" src="https://github.com/user-attachments/assets/8be2100e-5b3b-4b0d-ae5a-f4889c40ee7e">

As we can see there isn't any duplicate.

---

#### 2. Find missing values
```SQL
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
```SQL
# Ensure that the Sex column contains only valid entries ('Female' or 'Male').

SELECT *
FROM `my-project-438016.babyname.names`
WHERE Sex NOT IN ('Female', 'Male');
```
there is no duplicate.

---

#### 4. Check Rank and Count Values
```
SQL
# Ensure that the Rank is positive and within a reasonable range (e.g., 1 to the total number of names).
# Ensure that the Count is non-negative.

SELECT *
FROM `my-project-438016.babyname.names`
WHERE Rank <= 0 
   OR Count < 0;
```
There is no data to display.

---

#### 5. Standardise Name Format
```SQL
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
SQL
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
```SQL
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

#### 2. Names with Largest Growth or Decline
I want to identify names that have experienced the largest growth or decline in popularity between 1960 and 2022. This analysis highlights names that have seen significant shifts, either becoming more trendy or fading in popularity.
```SQL
WITH NameGrowth AS (
  SELECT Name, 
         SUM(CASE WHEN Year = 2022 THEN Count ELSE 0 END) AS Count_2022,
         SUM(CASE WHEN Year = 1960 THEN Count ELSE 0 END) AS Count_1960
  From my-project-438016.babyname.names
  GROUP BY Name
)
SELECT Name, 
       Count_2022, 
       Count_1960,
       ((Count_2022 - Count_1960) / NULLIF(Count_1960, 0)) * 100 AS Percentage_Change
FROM NameGrowth
WHERE Count_1960 > 0
ORDER BY Percentage_Change DESC;
```

#### 2.1. Names with Partial Decline:
Daniel and James saw a significant but partial decline in popularity:
#### 2.2. Names with Complete Disappearance:
Several names, such as Lisa, Susan, Michael, Mary, David, and John, were used frequently in 1960 but dropped to 0 occurrences in 2022. This represents a 100% decline, indicating that these names completely fell out of use in this period.

---

#### 3. Most Common Name per Year
I want to find out which name was the most popular each year. This helps in identifying the dominant baby name in different years, providing insights into yearly trends.
```SQL
WITH MaxCounts AS (
    SELECT Year, MAX(Count) AS Max_Count
    FROM my-project-438016.babyname.names
    WHERE Sex = 'Female'
    GROUP BY Year
)

SELECT n.Year, n.Name, n.Count
FROM my-project-438016.babyname.names n
JOIN MaxCounts m ON n.Year = m.Year AND n.Count = m.Max_Count
WHERE n.Sex = 'Female'
ORDER BY n.Year;

```
##### Summary of the Most Popular Female Baby Names (1997 - 2022) based on the code above:
- **Jessica** was the most popular name in 1997 and 1998 but lost the top spot by 1999.
- **Emily** took over from **Jessica** and remained the most popular name from 1999 to 2007. She stayed at the top for a full 9 years.
- **Isabella** became the most popular in 2008 and held the top position through 2010.
- **Sophia** was the leading name from 2011 to 2015, dominating for 5 consecutive years.
- **Mia** appeared as the most popular name in 2016, but only for that year.
- **Emma** was the most popular name in 2017 and 2018.
- **Olivia** then became the most popular name in 2019 and has stayed at the top through 2022, holding the top spot for 4 consecutive years.
##### Key Trends:
**Emily** and **Sophia** had the longest streaks of popularity (9 and 5 years, respectively).
**Olivia** is the current reigning name, having held the top spot for the past 4 years.

---





