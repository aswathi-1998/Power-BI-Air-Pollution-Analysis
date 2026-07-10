# Air Pollution Analysis
With publicly available data from 2021-2026, analyzing the concentration of various pollutants in the air, emphasizing on Keralam, India

# The Problem
With rising global temperatures and unepexpected changes in the environment, it is imperative that we look towards the root causes of factors that make our environment off  balance. Air is one factor that everyone needs - it shows no difference between the rich and poor, developed and developing. Clean air is thus imperative for a healthy living. Only when the citizens of a country is healthy can it become truly developed.

Keeping this in mind, I have created an air pollution report in Power BI, tracking the concentration of 6 air pollutants across various substations in the state of Keralam, India. The aim is to identify the patterns of air pollutants, see where the points of concern are, and to identify any possible solutions for it.

# Step 1 : Sourcing the Data
All data is collected from publically available sources via the [Central Pollution Control Board website](https://cpcb.gov.in/) . **Import** mode was used to collect the [daily data](https://airquality.cpcb.gov.in/ccr/#/caaqm-dashboard-all/advance-search) from 2021-2026 collected which was further cleaned before analysis.

Archive sources : https://airquality.cpcb.gov.in/ccr/#/repository/data

# Step 2 : Cleaning the data
The source contained information about 8 pollutants - CO, SO2, NH3, PM 10, PM 2.5, Ozone, NO - amongst other weather related data. Out of which according to CPCB Standards, 2 of them (Ozone and CO), needed to be analyzed ant an 8 hour average while the rest at a 24 hour avaerage. For simplicity sake I have only considered the 6 pollutants. Accrdingle by CPCB standards, AQI can only be calculated when there is data about atleast 3 of the pollutants, with one being either PM 2.5 or PM 10

To clean the data, Power Query was used. and the following steps where undertaken:

1. Removing unwanted beginning rows.
2. Selecting the required columns for analysis. (Dropping unwatnted columns like weather information since I had imported the entire dataset.
3. Ensuring the correct column names (removing the unit names from the column headers)
4. Replacing NA with ```null``` so that Power BI understands that the cell has blank values
5. With the help of a helper column, removing rows where both PM 2.5 and PM 10 are null (since AQI cant be calculated for those days)
6. Again with the help of a helper column, removing days where less than 3 pollutant information is available
7. Adding the station Name to each table
<img width="1365" height="720" alt="image" src="https://github.com/user-attachments/assets/90089931-0052-484d-9a18-a443fc9128bb" />
8. Appending queries to bring a consolidated table for each station (as different years corresponded to different rows)
9. Creation of Fact Table by Appending all station wise consolidated tables
<img width="1363" height="715" alt="image" src="https://github.com/user-attachments/assets/b04953ce-99b3-4994-acb3-72daf92880d9" />
10. Creation of a station Dimension table

# Step 3 : Creating the model

# Step 4 : Creating Measures

# Step 5 : Creating the visualizations

# Key Findings

# Challenges

# Future work
