# Pandas Challenge

In this challenge, I created and manipulates Pandas DataFrames to analyze school and standardized test data.

## Contents
- [Background](#background)
- [Instructions](#instructions)
    - [District Summary](#district-summary)
    - [School Summary](#school-summary)
    - [Highest-Performing Schools (by % Overall Passing)](#highest-performing-schools-by-%-overall-passing)
    - [Lowest-Performing Schools (by % Overall Passing)](#lowest-performing-schools-by-%-overall-passing)
    - [Math Scores by Grade](#math-scores-by-grade)
    - [Reading Scores by Grade](#reading-scores-by-grade)
    - [Scores by School Spending](#scores-by-school-spending)
    - [Scores by School Size](#scores-by-school-size)
    - [Scores by School Type](#scores-by-school-type)

## Background

As an exercise, I imagined I was the Chief Data Scientist for my city's school district. In this capacity, I would be helping the school board and mayor make strategic decisions regarding future school budgets and priorities.

As a first task, I had been asked to analyze the district-wide standardized test results. I was given access to every student's math and reading scores, as well as various information on the schools they attend. My task was to aggregate the data to showcase obvious trends in school performance. Here is how I aggragated the data:

```python
# Modules
from pathlib import Path
from textwrap import dedent
import pandas as pd

# Load and read School and Student Data File and store into Pandas DataFrames
school_data = pd.read_csv("Resources/schools_complete.csv")
student_data = pd.read_csv("Resources/students_complete.csv")

# Combine the data into a single dataset.  
school_data_complete = pd.merge(
    student_data, 
    school_data, 
    how="left", 
    on=["school_name", "school_name"])

school_data_complete.head()
```

Here's a look at the dataframe:

![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/6e6883bb-516f-40a5-9840-9db7d0533850)


Using Pandas and Jupyter Notebook, I needed to create a report that includes the data. My report needed to include a written description of at least two observable trends based on the data.

### District Summary

First, I performed some necessary calculations and then created a high-level snapshot of the district's key metrics in tue DataFrame. I discoverd the following using the referenced code:

- Total number of unique schools: 15
  ```python
  # Calculate the total number of unique schools
  school_count = school_data_complete["school_name"].nunique()
  school_count
  ```

- Total students: 39,170
```python
# Calculate the total number of students
student_count = school_data_complete["student_name"].count()
student_count
```

- Total budget: 24,649,428
```python
# Calculate the total budget for the district
total_budget = school_data["budget"].sum()
total_budget
```

- Average math score: 78.99
```python
# Calculate the average (mean) math score
average_math_score = student_data["math_score"].mean()
average_math_score
```

- Average reading score: 81.88
```python
# Calculate the average (mean) reading score
average_reading_score = student_data["reading_score"].mean()
average_reading_score
```
  
- % passing math (the percentage of students who passed math): 74.98
```python
# Calculate the percentage of students who passed math (math scores greather than or equal to 70)
passing_math_count = school_data_complete[(school_data_complete["math_score"] >= 70)].count()["student_name"]
passing_math_percentage = passing_math_count / float(student_count) * 100
passing_math_percentage
```

- % passing reading (the percentage of students who passed reading): 85.81
```python
# Calculate the percentage of students who passed reading (reading scores greather than or equal to 70)
passing_reading_count = school_data_complete[(school_data_complete["reading_score"] >= 70)].count()["student_name"]
passing_reading_percentage = passing_reading_count / float(student_count) * 100
passing_reading_percentage
```  

- % overall passing (the percentage of students who passed math AND reading): 65.17
```python
# Calculate the percentage of students that passed math and reading
passing_math_reading_count = (
    school_data_complete[
        ((school_data_complete["math_score"] >= 70) 
        & (school_data_complete["reading_score"] >= 70))]
        .count()["student_name"])
overall_passing_rate = passing_math_reading_count /  float(student_count) * 100
overall_passing_rate
```

### School Summary

Perform the necessary calculations and then create a DataFrame that summarizes key metrics about each school. Include the following:

- School name
- School type
- Total students
- Total school budget
- Per student budget
- Average math score
- Average reading score
- % passing math (the percentage of students who passed math)
- % passing reading (the percentage of students who passed reading)
- % overall passing (the percentage of students who passed math AND reading)

### Highest-Performing Schools (by % Overall Passing)

Sort the schools by % Overall Passing in descending order and display the top 5 rows. Save the results in a DataFrame called "top_schools".

### Lowest-Performing Schools (by % Overall Passing)

Sort the schools by % Overall Passing in ascending order and display the top 5 rows. Save the results in a DataFrame called "bottom_schools".

### Math Scores by Grade

Perform the necessary calculations to create a DataFrame that lists the average math score for students of each grade level (9th, 10th, 11th, 12th) at each school.

### Reading Scores by Grade

Create a DataFrame that lists the average reading score for students of each grade level (9th, 10th, 11th, 12th) at each school.

### Scores by School Spending

Create a table that breaks down school performance based on average spending ranges (per student). Use the code provided below to create four bins with reasonable cutoff values to group school spending.

```python
spending_bins = [0, 585, 630, 645, 680]
labels = ["<$585", "$585-630", "$630-645", "$645-680"]
```

Use pd.cut to categorize spending based on the bins.

Use the following code to then calculate mean scores per spending range.

```python
spending_math_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Math Score"]
spending_reading_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]
spending_passing_math = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]
spending_passing_reading = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]
overall_passing_spending = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Overall Passing"]
```

Use the scores above to create a DataFrame called `spending_summary`. Include the following metrics in the table:

- Average math score
- Average reading score
- % passing math (the percentage of students who passed math)
- % passing reading (the percentage of students who passed reading)
- % overall passing (the percentage of students who passed math AND reading)

**Scores by School Size**

Use the following code to bin the `per_school_summary`.

```python
size_bins = [0, 1000, 2000, 5000]
labels = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]
```

Use `pd.cut` on the "Total Students" column of the `per_school_summary` DataFrame. Create a DataFrame called `size_summary` that breaks down school performance based on school size (small, medium, or large).

**Scores by School Type**

Use the `per_school_summary` DataFrame from the previous step to create a new DataFrame called `type_summary`. This new DataFrame should show school performance based on the "School Type".


