# Case Study Analysis

## Introduction
In this case study, I analyze historical data from a Chicago-based bike-share company to identify trends in how their customers use bikes differently.

## Scenario
Cyclistic is a bike-share company based in Chicago with two types of customers. Customers who purchase single-ride or full-day passes are known as casual riders, while those who purchase annual memberships are known as members. Cyclistic’s financial analysts have concluded that annual members are much more profitable than casual riders. The director of marketing believes the company’s future success depends on maximizing the number of annual memberships.

The marketing analytics team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, the team will design a new marketing strategy to convert casual riders into annual members. The primary stakeholders for this project include Cyclistic’s director of marketing and the Cyclistic executive team. The Cyclistic marketing analytics team are secondary stakeholders.

## Defining the Problem
The main problem for the director of marketing and marketing analytics team is this: design marketing strategies aimed at converting Cyclistic’s casual riders into annual members.

There are three key questions for this project:
1. How do annual members and casual riders use Cyclistic bikes differently?
2. Why would casual riders buy Cyclistic annual memberships?
3. How can Cyclistic use digital media to influence casual riders to become members?

## Business Task
Analyze historical bike trip data to identify trends in how annual members and casual riders use Cyclistic bikes differently.

## Data Sources
We’ll be using Cyclistic’s historical bike trip data from the last 12 months, which is publicly available [here](https://divvy-tripdata.s3.amazonaws.com/index.html). The data is made available by Motivate International Inc. under this [license](https://divvybikes.com/data-license-agreement). The data is stored in 12 .CSV files:

- 2021-01_divvy_trip-data.csv
- 2021-02_divvy_trip-data.csv
- 2021-03_divvy_trip-data.csv
- 2021-04_divvy_trip-data.csv
- 2021-05_divvy_trip-data.csv
- 2021-06_divvy_trip-data.csv
- 2021-07_divvy_trip-data.csv
- 2021-08_divvy_trip-data.csv
- 2021-09_divvy_trip-data.csv
- 2021-10_divvy_trip-data.csv
- 2021-11_divvy_trip-data.csv
- 2021-12_divvy_trip-data.csv

The data is structured in rows (records) and columns (fields). Each record represents one trip, and each trip has a unique field that identifies it: ride_id. Each trip is anonymized and includes the following fields:

ride_id: Ride id - unique
rideable_type: Bike type - Classic, Docked, Electric
started_at: Trip start day and time
ended_at: Trip end day and time
start_station_name: Trip start station
start_station_id: Trip start station id
end_station_name: Trip end station
end_station_id: Trip end station id
start_lat: Trip start latitude
start_lng: Trip start longitude
end_lat: Trip end latitude
end_lng: Trip end longitude
member_casual: Rider type - Member or Casual

Additionally, bike station data made publicly available by the city of Chicago can be downloaded [here](https://data.cityofchicago.org/Transportation/Divvy-Bicycle-Stations/bbyy-e7gq/data).

Reliable and original: This is public data that contains accurate, complete, and unbiased info on Cyclistic’s historical bike trips. It can be used to explore how different customer types are using Cyclistic bikes.
Comprehensive and current: These sources contain all the data needed to understand the different ways members and casual riders use Cyclistic bikes. The data is from the past 12 months. It is current and relevant to the task at hand. This is important because the usefulness of data decreases as time passes.
Cited: These sources are publicly available data provided by Cyclistic and the City of Chicago. Governmental agency data and vetted public data are typically good sources of data.


## Data Cleaning and Manipulation

### Microsoft Excel: Initial Data Cleaning and Manipulation
Our next step is making sure the data is stored appropriately and prepared for analysis. After downloading all 12 zip files and unzipping them, I housed the files in a temporary folder on my desktop. I also created subfolders for the .CSV files and the .XLS files so that I have a copy of the original data. Then, I launched Excel, opened each file, and chose to Save As an Excel Workbook file. For each .XLS file, I did the following:

Changed the format of started_at and ended_at columns
Formatted as custom DATETIME: Format > Cells > Custom > yyyy-mm-dd h:mm:ss
Created a column called ride_length: Calculated the length of each ride by subtracting the column started_at from the column ended_at (example: =D2-C2)
Formatted as TIME: Format > Cells > Time > HH:MM:SS (37:30:55)
Created a column called ride_date: Calculated the date of each ride started using the DATE command (example: =DATE(YEAR(C2),MONTH(C2),DAY(C2)))
Formatted as Date: Format > Cells > Date > YYYY-MM-DD
Created a column called ride_month: Entered the month of each ride and formatted as number (example: January: =1) Format > Cells > Number
Created a column called ride_year: Entered the year of each ride and formatted as general Format > Cells > General > YYYY
Created a column called start_time: Calculated the start time of each ride using the started_at column Formatted as TIME Format > Cells > Time > HH:MM:SS (37:30:55)
Created a column called end_time: Calculated the end time of each ride using the ended_at column Formatted as TIME Format > Cells > Time > HH:MM:SS (37:30:55)
Created a column called day_of_week: Calculated the day of the week that each ride started using the WEEKDAY command (example: =WEEKDAY(C2,1)) Formatted as a NUMBER with no decimals Format > Cells > Number (no decimals) > 1,2,3,4,5,6,7
Note: 1 = Sunday and 7 = Saturday. After making these updates, I saved each .XLS file as a new .CSV file.

### BigQuery: Further Data Cleaning via SQL
Since these datasets are so large, it makes sense to move our analysis to a tool that is better suited for handling large datasets. I chose to use SQL via BigQuery. In order to continue processing the data in BigQuery, I created a bucket in Google Cloud Storage to upload all 12 files. I then created a project in BigQuery and uploaded these files as datasets. I’ve provided my initial cleaning and transformation SQL queries here for reference: initial_setup_query.sql.

CREATE QUARTERLY TABLES
In order to perform analysis by season, I combined these tables. I created Q1, Q2, Q3, and Q4 tables for analysis.

Table 1: 2021_Q1 -> JAN(01), FEB(02), MAR(03)
Table 2: 2021_Q2 -> APR(04), MAY(05), JUN(06)
Table 3: 2021_Q3 -> JUL(07), AUG(08), SEP(09)
Table 4: 2021_Q4 -> OCT(10), NOV(11), DEC(12)

I first created 2021_Q1 and then repeat for the remaining four tables(2,3, and 4):

-- Using UNION to join 2021_Q1 tables: JAN(01), FEB(02), MAR(03)
SELECT 
    *
  AS quarter1
FROM 
    `tripdata-429912.2021_01_tripdata.2021_01_tripdata`
UNION DISTINCT
  
SELECT 
  *
 AS quarter1
FROM 
    `tripdata-429912.2021_02_tripdata.2021_02_tripdata`
UNION DISTINCT
  
SELECT 
   *
 AS quarter
FROM 
    `tripdata-429912.2021_03_tripdata.2021_03_tripdata`


AGGREGATE QUARTERLY TABLES INTO A YEARLY TABLE
-- Creating 2022_yearly table by combining all four quarters
CREATE OR REPLACE TABLE `tripdata-429912.yearly_2021.yearly_2021` AS
SELECT
*
FROM 
`tripdata-429912.tripdata_quater_1.tripdata_quater_1`
UNION DISTINCT  
SELECT 
*
FROM
`tripdata-429912.tripdata_quater_2.tripdata_quater_2`
UNION DISTINCT
SELECT
*
FROM
`tripdata-429912.tripdata_quater_3.tripdata_quater_3`
UNION DISTINCT
SELECT
*
FROM
`tripdata-429912.tripdata_quater_4.tripdata_quater_4`

Refer to the SQL files for detailed quarterly analysis:

## Quarterly and Yearly Analysis
For quarterly and yearly analysis, the tables were combined using SQL. Full details of the analysis can be found here:
- [Analysis Q1 2021](https://github.com/Lawzyee/Bike_Share_Analysis_By_Ltechio/blob/6baba39ae3c5e84533dc1dc06758b4e41482dc88/Quarter_1_Analysis.csv)
- [Analysis Q2 2021](https://github.com/Lawzyee/Bike_Share_Analysis_By_Ltechio/blob/6baba39ae3c5e84533dc1dc06758b4e41482dc88/Quarter_2_Analysis.csv)
- [Analysis Q3 2021](https://github.com/Lawzyee/Bike_Share_Analysis_By_Ltechio/blob/6baba39ae3c5e84533dc1dc06758b4e41482dc88/Quarter_3_Analysis.csv)
- [Analysis Q4 2021](https://github.com/Lawzyee/Bike_Share_Analysis_By_Ltechio/blob/6baba39ae3c5e84533dc1dc06758b4e41482dc88/Quarter_4_Analysis.csv)
- [Full Year Analysis](https://github.com/Lawzyee/Bike_Share_Analysis_By_Ltechio/blob/6baba39ae3c5e84533dc1dc06758b4e41482dc88/Yearly_Analysis.csv)

## Summary and Visualizations
For a comprehensive summary and visualization of the analysis, refer to the following resources:
- [Power BI Dashboard](https://acrobat.adobe.com/id/urn:aaid:sc:EU:b61982ce-402c-4852-85fa-286b4c296572)
- [PDF Power BI Dashboard View](https://drive.google.com/file/d/1OeKyqA4_PVl7oGz-2N-vFV2nKtd4b79l/view?usp=sharing)
- [Presentation Slides](https://docs.google.com/presentation/d/11l_9LT7s9wgxNEzgAodb_s4YsVQOk_TI-vttlICKVpc/edit?usp=sharing)

## Conclusion
This analysis identifies key patterns in how annual members and casual riders use Cyclistic bikes differently. The insights gathered will inform marketing strategies aimed at converting casual riders into annual members, helping Cyclistic increase profitability and fuel future growth.
