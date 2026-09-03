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
<img width="652" height="449" alt="image" src="https://github.com/user-attachments/assets/ce3f6da8-29a5-44d1-92af-c55a711ca1a3" />
What was used at core was a star schema, with the dimension tab being a dedicated dates table for easier performance of time intelligence

# Step 4 : Creating Measures
A dedicated measures table was created to organize the measures for easy acces. For each Pollutant, the relevant sub index was calculated using the prescribed formula by CPCB. The maximum value of these subindices is considered to be the overall AQI.
Some of the measures created wre:
```
AQI SO2 = 
VAR _IHi = 
        SWITCH(
            TRUE(),
            [SO2 Avg] <= 40, 50,
            [SO2 Avg] <= 80, 100,
            [SO2 Avg] <= 380, 200,
            [SO2 Avg] <= 800,300,
            [SO2 Avg] <= 1600,400,
            500)

VAR _ILo = 
        SWITCH(
            TRUE(),
            [SO2 Avg] <= 40, 0,
            [SO2 Avg] <= 80, 51,
            [SO2 Avg] <= 380, 101,
            [SO2 Avg] <= 800,201,
            [SO2 Avg] <= 1600,301,
            401)
VAR _BPHi = 
        SWITCH(
            TRUE(),
            [SO2 Avg] <= 40, 40,
            [SO2 Avg] <= 80, 80,
            [SO2 Avg]<= 380, 380,
            [SO2 Avg] <= 800,800,
            [SO2 Avg] <= 1600,1600,
            3200)
VAR _BPLo = 
        SWITCH(
            TRUE(),
            [SO2 Avg] <= 40, 0,
            [SO2 Avg] <= 80, 47,
            [SO2 Avg] <= 380, 81,
            [SO2 Avg]<= 800,381,
            [SO2 Avg] <= 1600,801,
            1600)

VAR _Conc = ROUND([SO2 Avg],0)
RETURN
IF([SO2 Avg] <> BLANK(),
(DIVIDE(_IHi - _ILo, _BPHi - _BPLo) * (_Conc - _BPLo)) + _ILo)

```
```
AQI Max = 
VAR _List = {[AQI NH3],[AQI NO2],[AQI PM10],[AQI PM2.5],[AQI SO2]}
RETURN MAXX(_List,[Value])
```
# Step 5 : Creating the visualizations
 <table>
  <tr>
    <td><img width="420" alt="screenshot1" src="https://github.com/user-attachments/assets/5eff5222-12a4-4ff6-9bf0-e49bd152eb51" /></td>
    <td><img width="420" alt="screenshot2" src="https://github.com/user-attachments/assets/d2690e65-ec7b-47a2-835e-2e534b0f12b2" /></td>
  </tr>
  <tr>
    <td><img width="420" alt="screenshot3" src="https://github.com/user-attachments/assets/7685fb81-fc41-4532-928a-2fe12179f296" /></td>
    <td><img width="420" alt="screenshot4" src="https://github.com/user-attachments/assets/1f95f9e1-dd90-4af0-b81a-a591995e4f79" /></td>
  </tr>
  <tr>
    <td><img width="420" alt="screenshot5" src="https://github.com/user-attachments/assets/41a724b0-c3c9-410e-a45a-95514ab7f665" /></td>
    <td></td>
  </tr>
</table>

# Key Findings

- Most prevalent pollutants were PM 2.5 AND PM 10.
- The AQI levels showed cyclical patterns – a decreasing pattern from March-April to Early October followed by an increase.
- Overall, the AQI remained in the ‘Satisfactory’ levels (between 50 and 100), with rare spikes above that level.
- Crowded areas like Vytilla shows relatively higher AQI, possibly indicating the influence of heavy traffic congestion to poor air quality.
- Unnatural spike in Plammoodu station in summer 2025, on searching through social media, the reason was attributed to a faulty meter.


# Challenges

- Poor documentation of data - out of the 9 CAAQMS stations, only data pertaining to 7 were available, of which for Vyttilla, the data was only available till 2024
- I found it difficult to integrate the  live data via an API, hence import mode was used after downloading the data into the local computer, Hence owing to limitations in space, only Kerala was considered
- CO and O3 required an 8 hour window average while the rest used a 24 hour window. Thus these 2 were not integrated as even in official website, for Keralam, these were not the major cause of pollution
  
# Future work
- Increase the number of pollutants analyzed (CO and Ozone)
- Use live data for real time analysis
- Compare performance with other states

