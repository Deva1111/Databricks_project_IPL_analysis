﻿# Databricks_project_IPL_analysis

# IPL Data Analysis Project

This project utilizes Apache Spark for analyzing IPL cricket match data. The analysis is conducted using PySpark and is run on Databricks.

## Project Structure

- **schemas**: Defines the structure of the data for various CSV files.
- **data**: Contains IPL match data in CSV format.
- **transformations**: Performs data transformations and aggregations.

## Setup and Installation

1. **Clone the repository**:
    ```sh
    git clone https://github.com/your-username/ipl-data-analysis.git
    ```
2. **Open Databricks and create a new cluster**.
3. **Upload the CSV files to Databricks File System (DBFS)** or connect to your cloud storage.
4. **Create a new notebook** in Databricks and copy the code from the repository.

## Data Sources

- **Ball_By_Ball.csv**: Contains ball-by-ball details of IPL matches.
- **Match.csv**: Contains match-level details.
- **Player.csv**: Contains player information.
- **Player_match.csv**: Contains player performance in each match.
- **Team.csv**: Contains team information.

## Schemas

The schemas for the data files are defined using `StructType` and `StructField` in PySpark, ensuring correct data types and structure.

## Data Loading

Data is loaded into Spark DataFrames from CSV files using the `spark.read` function with specified schemas.

## Data Transformation

- **Filtering**: Excludes invalid deliveries such as wides and no-balls for specific analyses.
- **Aggregation**: Calculates total and average runs scored in each match and inning.
- **Window Function**: Computes the running total of runs for each match and inning.
- **Conditional Column**: Flags high-impact balls (either a wicket or more than 6 runs including extras).

## Example Analysis

```python
# Calculate the total and average runs scored in each match and inning
total_and_avg_runs = ball_by_ball_df.groupBy("match_id","innings_no").agg(
    sum("runs_scored").alias("total_runs"),
    avg("runs_scored").alias("average_runs")
)

# Calculate the running total of runs in each match for each over
windowSpec = Window.partitionBy("match_id","innings_no").orderBy("over_id")
ball_by_ball_df  = ball_by_ball_df.withColumn(
    "running_total_runs",
    sum("runs_scored").over(windowSpec)
)

# Flag high impact balls
ball_by_ball_df = ball_by_ball_df.withColumn(
    "high_impact",
    when((col("runs_scored")+ col("extra_runs")>6) | (col("bowler_wicket") == True), True).otherwise(False)
)
```

## Viewing Data

To view the transformed data:

```python
ball_by_ball_df.show(5)
```

