Aviation Accidents Python Analysis
Project Goal
My project aims to determine whether the aviation business would be a good new venture for the company by finding out:

The seasons when most accidents occur.
Identify the safest Aircraft model for Investment and
Gain competitive Advantage and customer trust.
I used 23 columns which provided variables during analysis and tittles in my visualisations.

Data Source
My data comes from [kaggle](https://www.kaggle.com/datasets/khsamaha/aviation-accident-database-synopses/data)  by the NTSB aviation accident database containing information about civil aviation accidents and selected incidents within the United States and some countries.
Step 1: Importing libraries and loading Data¶
`# Import data analysis libraries
import pandas as pd
import numpy as np
from matplotlib import pyplot as plt
from numbers import Number
import seaborn as sns
​
from warnings import filterwarnings
filterwarnings("ignore")

#Read dataset
df = pd.read_csv('./AviationData.csv.zip', encoding = 'latin1')
print(df.head()) # Check the first 5 rows
assert type(df) == pd.DataFrame`

Data Cleaning
During data cleaning, I started by removing columns that were not applicable for analysis.
`#Drop irrelevant columns
columns_to_drop = ['Investigation.Type', 'Accident.Number', 'Report.Status', 'Publication.Date', 'Aircraft.Damage', 'Air.Carrier', 
    'Schedule', 'FAR.Description', 'Registration.Number' ]
df = df.drop(columns=columns_to_drop, errors='ignore')  # Ignore if columns don't exist
print(df.info())
print(df.shape)`

Then I was able to remove missing values
`# Drop rows with critical missing values (e.g., no aircraft model or date)
df = df.dropna(subset=['Make', 'Model', 'Event.Date', 'Total.Fatal.Injuries'])

#Fill numerical missing values (e.g., injuries) with 0
df['Total.Fatal.Injuries'] = df['Total.Fatal.Injuries'].fillna(0)
df['Total.Serious.Injuries'] = df['Total.Serious.Injuries'].fillna(0)
print(df.shape)
print(df.info())`

Data Analysis and Visualization
1: Total accidents over time
We need to find out the distribution of the accidents as the years progressed
`# Extract year from 'Event.Date'
df['Year'] = pd.to_datetime(df['Event.Date']).dt.year

#Plot accidents per year
plt.figure(figsize=(12, 8))
sns.countplot(data=df, x='Year', palette='viridis')
plt.title('Aviation Accidents per Year (1982-2023)')
plt.xticks(rotation=90)
plt.show()`

Aircraft-Specific Risk Analysis
(i). Top 10 Safest Aircraft (Lowest Accident Rates)
`# Group by aircraft make/model and count accidents
aircraft_safety = df.groupby(['Make', 'Model']).agg(
    Total_Accidents=('Is.Fatal', 'count'),
    Fatal_Accidents=('Is.Fatal', 'sum')
).reset_index()

#Calculate fatal accident rate
aircraft_safety['Fatal_Rate'] = (aircraft_safety['Fatal_Accidents'] / aircraft_safety['Total_Accidents']) * 100

#Filter aircraft with at least 10 recorded accidents (for statistical significance)
significant_aircraft = aircraft_safety[aircraft_safety['Total_Accidents'] >= 10]

#Top 10 safest (lowest fatal rate)
top10_safest = significant_aircraft.sort_values('Fatal_Rate').head(10)
print(top10_safest)`

(ii): Top 10 Riskiest Aircraft (Highest Fatal Rates)
`# Top 10 riskiest (highest fatal rate)
top10_risky = significant_aircraft.sort_values('Fatal_Rate', ascending=False).head(10)
print(top10_risky)`

I used `for loop` to find seasons from `Event.Date` then I plotted.
`# Extract month from date
weather_accidents['Month'] = pd.to_datetime(df['Event.Date']).dt.month

#Initialize an empty list to store seasons
seasons = []

#Assign season for each month using a for loop
for month in weather_accidents['Month']:
    if month in [12, 1, 2]:
        seasons.append('Winter')
    elif month in [3, 4, 5]:
        seasons.append('Spring')
    elif month in [6, 7, 8]:
        seasons.append('Summer')
    else:
        seasons.append('Autumn')

#Add the seasons list as a new column
weather_accidents['Season'] = seasons

#Plot accidents by season
plt.figure(figsize=(10, 6))
sns.countplot(data=weather_accidents, x='Season', order=['Winter', 'Spring', 'Summer', 'Autumn'])
plt.title('Weather-Related Accidents by Season')
plt.xlabel('Season')
plt.ylabel('Number of Accidents')
plt.show()`
