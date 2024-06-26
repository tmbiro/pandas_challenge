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

First, I performed some necessary calculations and then created a high-level snapshot of the district's key metrics in the DataFrame. I discoverd the following using the referenced code:

- <b>Total number of unique schools:</b> 15
  ```python
  # Calculate the total number of unique schools
  school_count = school_data_complete["school_name"].nunique()
  ```

- <b>Total students:</b> 39,170
    ```python
    # Calculate the total number of students
    student_count = school_data_complete["student_name"].count()
    ```

- <b>Total budget:</b> $24,649,428
    ```python
    # Calculate the total budget for the district
    total_budget = school_data["budget"].sum()
    ```

- <b>Average math score:</b> 78.99
    ```python
    # Calculate the average (mean) math score
    average_math_score = student_data["math_score"].mean()
    ```

- <b>Average reading score:</b> 81.88
    ```python
    # Calculate the average (mean) reading score
    average_reading_score = student_data["reading_score"].mean()
    ```
  
- <b>% passing math (the percentage of students who passed math):</b> 74.98
    ```python
    # Calculate the percentage of students who passed math (math scores greather than or equal to 70)
    passing_math_count = school_data_complete[(school_data_complete["math_score"] >= 70)].count()["student_name"]
    passing_math_percentage = passing_math_count / float(student_count) * 100
    ```

- <b>% passing reading (the percentage of students who passed reading):</b> 85.81
    ```python
    # Calculate the percentage of students who passed reading (reading scores greather than or equal to 70)
    passing_reading_count = school_data_complete[(school_data_complete["reading_score"] >= 70)].count()["student_name"]
    passing_reading_percentage = passing_reading_count / float(student_count) * 100
    ```  

- <b>% overall passing (the percentage of students who passed math AND reading):</b> 65.17
    ```python
    # Calculate the percentage of students that passed math and reading
    passing_math_reading_count = (
        school_data_complete[
            ((school_data_complete["math_score"] >= 70) 
            & (school_data_complete["reading_score"] >= 70))]
            .count()["student_name"])
    overall_passing_rate = passing_math_reading_count /  float(student_count) * 100
    ```
Here's a high-level snapshot of the district's key metrics in a DataFrame

![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/84518044-2b99-4f7d-8521-20a2c5625b4a)

I used this code to get it:
```python
# Create a high-level snapshot of the district's key metrics in a DataFrame
district_summary = pd.DataFrame({
    "Total Schools": school_count,
    "Total Students": student_count,
    "Total Budget": total_budget,
    "Average Math Score": average_math_score,
    "Average Reading Score": average_reading_score,
    "% Passing Math": passing_math_percentage,
    "% Passing Reading": passing_reading_percentage,
    "% Overall Passing Rate": [overall_passing_rate]
})

# Formatting
district_summary["Total Students"] = district_summary["Total Students"].map("{:,}".format)
district_summary["Total Budget"] = district_summary["Total Budget"].map("${:,.2f}".format)

# Display the DataFrame
district_summary
```

### School Summary

Here, I created a DataFrame that summarizes key metrics about each school. I included the following (with code examples):

- <b>School name and School type</b>
    ```python
    # Set school name as index and select the school types
    school_types = school_data.set_index("school_name")["type"]
    ```

- <b>Total students</b>
    ```python
    # Calculate the total student count
    per_school_counts = school_data_complete.groupby(['school_name'])['Student ID'].count()
    ```
    
- <b>Total school budget</b>
    ```python
    # Calculate the total school budget
    per_school_budget = school_data_complete.groupby(["school_name"])["budget"].mean()
    ```
    
- <b>Per student budget</b>
    ```python
    # Calculate the per capita spending
    per_school_capita = per_school_budget / per_school_counts
    ```
    
- <b>Average math score</b>
    ```python
    # Calculate the average math scores for each school
    per_school_math = school_data_complete.groupby(["school_name"])["math_score"].mean()
    ```
    
- <b>Average reading score</b>
    ```python
    # Calculate the average reading scores for each school
    per_school_reading = school_data_complete.groupby(["school_name"])["reading_score"].mean()
    ```
    
- <b>% passing math (the percentage of students who passed math)</b>
    ```python
    # Calculate the percentage of students with math scores of 70 or higher in each school
    school_passing_math = school_data_complete[(school_data_complete["math_score"] >= 70)].groupby("school_name").count()["student_name"]
    school_passing_math_percentage = (school_passing_math / per_school_counts)*100
    ```
    
- <b>% passing reading (the percentage of students who passed reading)</b>
    ```python
    # Calculate the percentage of students with reading scores of 70 or higher in each school
    school_passing_reading = school_data_complete[(school_data_complete["reading_score"] >= 70)].groupby("school_name").count()["student_name"]
    school_passing_reading_percentage = (school_passing_reading / per_school_counts)*100
    ```
    
- <b>% overall passing (the percentage of students who passed math AND reading)</b>
    ```python
    # Calculate percentage of students that passed both math and reading with scores of 70 or higher for each school
    school_overall_passing_count = (
        school_data_complete[
            ((school_data_complete["math_score"] >= 70) 
            & (school_data_complete["reading_score"] >= 70))]
            .groupby("school_name")
            .count()["student_name"])
    school_overall_passing_rate = (school_overall_passing_count /  per_school_counts) * 100
    ```
Then, I created the dataframe:
```python
# Create a DataFrame called `per_school_summary` with columns for the calculations above.
per_school_summary = pd.DataFrame({
    "School Type": school_types,
    "Total Students": per_school_counts,
    "Total School Budget": per_school_budget,
    "Per Student Budget": per_school_capita,
    "Average Math Score": per_school_math,
    "Average Reading Score": per_school_reading,
    '% Passing Math': school_passing_math_percentage,
    '% Passing Reading': school_passing_reading_percentage,
    "% Overall Passing": school_overall_passing_rate
})

# Formatting
per_school_summary["Total School Budget"] = per_school_summary["Total School Budget"].map("${:,.2f}".format)
per_school_summary["Per Student Budget"] = per_school_summary["Per Student Budget"].map("${:,.2f}".format)

# Display the DataFrame
per_school_summary
```
![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/7580f1bc-bce2-415b-93ca-ddc4ae5afdb1)


### Highest-Performing Schools (by % Overall Passing)

Next, I sorted the schools by % Overall Passing in descending order and displayed the top 5 rows. I also saved the results in a DataFrame called "top_schools".
```python
# Sort the schools by `% Overall Passing` in descending order and display the top 5 rows.
per_school_summary = per_school_summary.sort_values("% Overall Passing", ascending=False)
top_schools = per_school_summary.head(5)
```
![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/fd42ae5e-c1b7-480a-8d83-5df69fda79cc)

We can see that students in charter schools performed the best.


### Lowest-Performing Schools (by % Overall Passing)

I also sorted the schools by % Overall Passing in ascending order and displayed the top 5 rows. I then saved the results in a DataFrame called "bottom_schools".
```python
# Sort the schools by `% Overall Passing` in ascending order and display the top 5 rows.
per_school_summary = per_school_summary.sort_values("% Overall Passing")
bottom_schools = per_school_summary.head(5)
```
![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/c1a89ec7-f66a-44c0-9e12-fff6f8099954)

We can see that students in district schools performed worst of all (they also seem to have a higher budget than the charter schools shown above).

### Math Scores by Grade

Then, I performed the necessary calculations to create a DataFrame that lists the average math score for students of each grade level (9th, 10th, 11th, 12th) at each school.
```python
# Separate the data by grade
ninth_graders = school_data_complete[(school_data_complete["grade"] == "9th")]
tenth_graders = school_data_complete[(school_data_complete["grade"] == "10th")]
eleventh_graders = school_data_complete[(school_data_complete["grade"] == "11th")]
twelfth_graders = school_data_complete[(school_data_complete["grade"] == "12th")]

# Group by "school_name" and take the mean of each.
ninth_graders_math_scores = ninth_graders.groupby("school_name")["math_score"].mean()
tenth_graders_math_scores = tenth_graders.groupby("school_name")["math_score"].mean()
eleventh_graders_math_scores = eleventh_graders.groupby("school_name")["math_score"].mean()
twelfth_graders_math_scores = twelfth_graders.groupby("school_name")["math_score"].mean()

# Combine each of the scores above into single DataFrame called `math_scores_by_grade`
math_scores_by_grade = pd.DataFrame({
    "9th": ninth_graders_math_scores,
    "10th": tenth_graders_math_scores,
    "11th": eleventh_graders_math_scores,
    "12th": twelfth_graders_math_scores
})

# Minor data wrangling
math_scores_by_grade.index.name = None

# Display the DataFrame
math_scores_by_grade
```
![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/695f3d6b-06b9-4969-8b33-76e45e34dec7)


### Reading Scores by Grade

I also created a DataFrame that lists the average reading score for students of each grade level (9th, 10th, 11th, 12th) at each school.
```python
# Group by "school_name" and take the mean of each.
ninth_graders_reading_scores = ninth_graders.groupby("school_name")["reading_score"].mean()
tenth_graders_reading_scores = tenth_graders.groupby("school_name")["reading_score"].mean()
eleventh_graders_reading_scores = eleventh_graders.groupby("school_name")["reading_score"].mean()
twelfth_graders_reading_scores = twelfth_graders.groupby("school_name")["reading_score"].mean()

# Combine each of the scores above into single DataFrame called `reading_scores_by_grade`
reading_scores_by_grade = pd.DataFrame({
    "9th": ninth_graders_reading_scores,
    "10th": tenth_graders_reading_scores,
    "11th": eleventh_graders_reading_scores,
    "12th": twelfth_graders_reading_scores
})

# Minor data wrangling
reading_scores_by_grade.index.name = None

# Display the DataFrame
reading_scores_by_grade
```
![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/bf23c38a-a949-44fc-a55e-3f5eba712b2c)

Grade-level differences seem minimal. Let's look at some more variables that might be influencing test scores.

### Scores by School Spending

I then created a table that breaks down school performance based on average spending ranges (per student).
```python
# Establish the bins 
spending_bins = [0, 585, 630, 645, 680]
spending_labels = ["<$585", "$585-630", "$630-645", "$645-680"]

# Create a copy of the school summary since it has the "Per Student Budget"
school_spending_df = per_school_summary.copy()
school_spending_df["Per Student Budget"] = school_spending_df["Per Student Budget"].str.replace('$', '', regex=True).astype(float)

# Use `pd.cut` to categorize spending based on the bins.
school_spending_df["Spending Ranges (Per Student)"] = pd.cut(school_spending_df["Per Student Budget"], spending_bins, labels=spending_labels, include_lowest=True)
school_spending_df.head()

#  Calculate averages for the desired columns. 
spending_math_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean(numeric_only=True)["Average Math Score"]
spending_reading_scores = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean(numeric_only=True)["Average Reading Score"]
spending_passing_math = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean(numeric_only=True)["% Passing Math"]
spending_passing_reading = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean(numeric_only=True)["% Passing Reading"]
overall_passing_spending = school_spending_df.groupby(["Spending Ranges (Per Student)"]).mean(numeric_only=True)["% Overall Passing"]

# Assemble into DataFrame
spending_summary = pd.DataFrame({
    "Average Math Score": spending_math_scores,
    "Average Reading Score": spending_reading_scores,
    '% Passing Math': spending_passing_math,
    '% Passing Reading': spending_passing_reading,
    "% Overall Passing": overall_passing_spending
    })

# Display results
spending_summary
```
![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/d8be3687-6b0e-4e3d-96eb-61ef76524b88)

We can see that students in schools with smaller spending ranges per student performed better on math and reading than students in schools with larger spending ranges.

**Scores by School Size**

Next, I created a DataFrame called `size_summary` that breaks down school performance based on school size (small, medium, or large).
```python
# Establish the bins.
size_bins = [0, 1000, 2000, 5000]
size_labels = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

# Categorize the spending based on the bins
# Use `pd.cut` on the "Total Students" column of the `per_school_summary` DataFrame.

per_school_summary["School Size"] = pd.cut(per_school_summary["Total Students"], size_bins, labels=size_labels, include_lowest=True)

# Calculate averages for the desired columns. 
size_math_scores = per_school_summary.groupby(["School Size"]).mean(numeric_only=True)["Average Math Score"]
size_reading_scores = per_school_summary.groupby(["School Size"]).mean(numeric_only=True)["Average Reading Score"]
size_passing_math = per_school_summary.groupby(["School Size"]).mean(numeric_only=True)["% Passing Math"]
size_passing_reading = per_school_summary.groupby(["School Size"]).mean(numeric_only=True)["% Passing Reading"]
size_overall_passing = per_school_summary.groupby(["School Size"]).mean(numeric_only=True)["% Overall Passing"]

# Create a DataFrame called `size_summary` that breaks down school performance based on school size (small, medium, or large).
# Use the scores above to create a new DataFrame called `size_summary`
size_summary = pd.DataFrame({
    "Average Math Score": size_math_scores,
    "Average Reading Score": size_reading_scores,
    '% Passing Math': size_passing_math,
    '% Passing Reading': size_passing_reading,
    "% Overall Passing": size_overall_passing
    })


# Display results
size_summary
```
![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/f4b6e0bc-2176-4251-8c01-c6304ce8b940)

We can see that smaller- and medium-sized schools performed better than larger-sozed schools.

**Scores by School Type**

Lastly, I created new DataFrame that showed school performance based on the "School Type" to confirm the observations that charter schools out-performed district schools.
```python

# Group the per_school_summary DataFrame by "School Type" and average the results.
type_math_scores = per_school_summary.groupby(["School Type"]).mean(numeric_only=True)["Average Math Score"]
type_reading_scores = per_school_summary.groupby(["School Type"]).mean(numeric_only=True)["Average Reading Score"]
type_passing_math = per_school_summary.groupby(["School Type"]).mean(numeric_only=True)["% Passing Math"]
type_passing_reading = per_school_summary.groupby(["School Type"]).mean(numeric_only=True)["% Passing Reading"]
type_overall_passing = per_school_summary.groupby(["School Type"]).mean(numeric_only=True)["% Overall Passing"]

# Assemble the new data by type into a DataFrame called `type_summary`
type_summary = pd.DataFrame({
    "Average Math Score": type_math_scores,
    "Average Reading Score": type_reading_scores,
    '% Passing Math': type_passing_math,
    '% Passing Reading': type_passing_reading,
    "% Overall Passing": type_overall_passing
    })

# Display results
type_summary
```
![image](https://github.com/tmbiro/pandas_challenge/assets/26468137/a4e5971f-e00d-4334-9a80-784fc856e746)

We can see that is the case: charter schools far out-performed district schools.
