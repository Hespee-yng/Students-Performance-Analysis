# Students Performance Analysis - SQL Project  

## Project Overview  

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a students performance database, performing exploratory data analysis (EDA), and answering specific questions using SQL queries. 

## Objectives

1. **Set up a students performance database**: Create and populate a students performance database with the downloaded students performance data.
2. **Data Cleaning**: Identify and remove any records with missing or null values, adjust incorrect spellings and duplicate files if any.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific questions and derive insights from the students performance data.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `studentsdb`.
- **Table Creation**: A table named `students_performance` is created to store the students performance data. The table structure includes columns for gender, race/ethnicity, parental level of education,	lunch, test preparation course, math score, reading score and writing score.

```sql
CREATE DATABASE studentsDB;;

CREATE TABLE students_performance (
    gender VARCHAR(8),
    race_or_ethnicity VARCHAR(10),
    parental_level_of_education VARCHAR(20),
    lunch VARCHAR(15),
    test_preparation_course VARCHAR(10),
    math_score INT,
    reading_score INT,
    writing_score INT
);
```

### 2. Data Exploration & Cleaning

- **Null Value Check**: Check for any null values in the dataset.
- **Unique Value Check**: Check unique values to spot potential spelling errors.
- **Record Count**: Determine the total number of records in the dataset.
- **Max, Min, Avg Scores Check**: Calculate the maximum, minimum and average of exam scores.

```sql
SELECT * FROM students_performance
WHERE
    gender IS NULL
    OR race_or_ethnicity IS NULL
    OR parental_level_of_education IS NULL
    OR lunch IS NULL
    OR test_preparation_course IS  NULL
    OR math_score IS NULL
    OR reading_score IS NULL
    OR writing_score IS NULL;

SELECT DISTINCT(gender) FROM students_performance;
SELECT DISTINCT(race_or_ethnicity) FROM students_performance;
SELECT DISTINCT(parental_level_of_education) FROM students_performance;
SELECT DISTINCT(lunch) FROM students_performance;
SELECT DISTINCT(test_preparation_course) FROM students_performance;
SELECT DISTINCT(reading_score) FROM students_performance;
SELECT DISTINCT(math_score) FROM students_performance;
SELECT DISTINCT(writing_score) FROM students_performance;

SELECT COUNT (*) FROM students_performance;

SELECT
    MAX(math_score) as max_math_score,
    MAX(reading_score) max_reading_score,
    MAX(writing_score) max_writing_score,
    MIN(math_score) min_math_score,
    MIN(reading_score) min_reading_score,
    MIN(writing_score) min_writing_score,
    AVG(math_score)::INT avg_math_score,
    AVG(reading_score)::INT avg_reading_score,
    AVG(writing_score)::INT avg_writing_score
FROM
    students_performance;
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer each questions:

1. **Write a query to list all students who scored above 90 in math.**:
```sql
SELECT
    *
FROM
    students_performance
WHERE
    math_score > 90
ORDER BY
    1;
```

2. **Write a query to find the number of students who completed the test preparation course.**:
```sql
SELECT
    COUNT(*) as number_of_students
FROM
    students_performance
WHERE
    test_preparation_course = 'completed';
```

3. **Write a query to calculate the average writing score for each parental level of education.**:
```sql
SELECT
    parental_level_of_education,
    AVG(writing_score)::INT as avg_writing_score
FROM
    students_performance
GROUP BY
    1;
```

4. **Write a query to find the highest and lowest scores in reading for each gender.**:
```sql
SELECT
    gender,
    MAX(reading_score) as highest_score,
    MIN(reading_score) as lowest_score
FROM
    students_performance
GROUP BY
    1;
```

5. **Write a query to count the number of students in each race/ethnicity group.**:
```sql
SELECT
    race_or_ethnicity,
    COUNT(*) as number_of_students
FROM
    students_performance
GROUP BY
    1
ORDER BY
    1;
```

6. **Write a query to determine the total number of students who scored above 70 in all subjects.**:
```sql
SELECT
    COUNT(*) as number_of_students
FROM
    students_performance
WHERE
    math_score > 70
    AND reading_score > 70
    AND writing_score > 70;
```

7. **Write a query to calculate the average math, reading, and writing scores for students who completed the test preparation course.**:
```sql
SELECT
    AVG(math_score)::INT as avg_math_score,
    AVG(reading_score)::INT as avg_reading_score,
    AVG(writing_score)::INT as avg_writing_score
FROM
    students_performance
WHERE
    test_preparation_course = 'completed';
```

8. **Write a query to find students who scored below 50 in at least one subject.**:
```sql
SELECT
    *
FROM
    students_performance
WHERE
    math_score < 50
    OR reading_score < 50
    OR writing_score < 50;
```

9. **Write a query to retrieve the records of students who scored above 80 in both reading and writing.**:
```sql
SELECT
    *
FROM
    students_performance
WHERE
    reading_score > 80
    AND writing_score > 80;
```

10. **Write a query to display the records of students with a math score above 90 but a reading score below 60.**:
```sql
SELECT
    *
FROM
    students_performance
WHERE
    math_score > 90
    AND reading_score < 60;	
```

11. **Write a query to identify students from group C who completed the test preparation course and scored above 85 in math.**:
```sql
SELECT 
    *
FROM
    students_performance
WHERE
    race_or_ethnicity = 'group C'
    AND test_preparation_course = 'completed'
    AND math_score > 85;
```

12. **Write a query to find all students whose scores are within the top 10% for each subject.**:
```sql
WITH percentile as (
    SELECT
        PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY math_score) as math_90th_percentile,
        PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY reading_score) as reading_90th_percentile,
        PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY writing_score) as writing_90th_percentile
    FROM
        students_performance
)
SELECT
    s.*
FROM
    students_performance s
    JOIN percentile c
        ON s.math_score > c.math_90th_percentile
            AND s.reading_score > c.reading_90th_percentile
            AND s.writing_score > c.writing_90th_percentile;
```

13. **Write a query to rank students by their total score (sum of math, reading, and writing scores).**:
```sql
SELECT 
    *,
    math_score + reading_score + writing_score as total_score,
    DENSE_RANK() OVER (ORDER BY math_score + reading_score + writing_score DESC) as rank
FROM
    students_performance;
```

14. **Write a query to group students by gender and calculate the percentage of students in each category who completed the test preparation course.**:
```sql
SELECT
    gender,
    ROUND(
        SUM(CASE
                WHEN test_preparation_course = 'completed' THEN 1
                ELSE 0
            END)::DECIMAL/COUNT(test_preparation_course), 2)*100 as percent_completed_test_course
FROM
    students_performance
GROUP BY
    1;
```

15. **Write a query to find the students whose math score is greater than the overall average math score.**:
```sql
WITH avg_math_score as (
    SELECT
        ROUND(AVG(math_score), 2) as avg_score
    FROM
        students_performance
)
SELECT
    *
FROM
    students_performance s
    JOIN avg_math_score a
        ON s.math_score > a.avg_score;
```

16. **Write a query to determine which demographic group has the highest proportion of high scorers (above 90 in all subjects).**:
```sql
WITH high_scorers AS (
    SELECT
        race_or_ethnicity,
        ROUND(
            SUM(CASE
                    WHEN math_score > 90 AND reading_score > 90 AND writing_score > 90 THEN 1
                    ELSE 0
                END)::DECIMAL/COUNT(race_or_ethnicity), 2) * 100 as percentage_high_scorers,
        dense_rank() OVER (ORDER BY 
            ROUND(
                SUM(CASE
                        WHEN math_score > 90 AND reading_score > 90 AND writing_score > 90 THEN 1
                        ELSE 0
                    END)::DECIMAL/COUNT(race_or_ethnicity), 2) * 100 DESC) AS rank
    FROM
        students_performance
    GROUP BY
        1)
SELECT
    race_or_ethnicity,
    percentage_high_scorers
FROM
    high_scorers
WHERE
    rank = 1;
```


## Findings

- **Top Scorers by Demographic**: Group E has the most proportion of top scorers (students who score above 90 in each subect).
- **Math Score**: Only 50 out of 1000 students scored more than 90 in math.
- **Test Preparation Course**: Among total of 1000 students, only 358 students took the test preparation course.
- **Scores Above 70**: 306 0f 1000 students scored more than 70 in each subect.
- **Top 10%**: 42 students are consistently in top 10% in each subect.
- **Best Performers**: 3 students earned full scores across all subjects.  
  
  
### Thank You.
- **LinkedIn**: [Connect with me professionally](https://www.linkedin.com/in/abdullahi-oyekanmi)
