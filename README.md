# Connecticut-Real-Estate-Analysis

## Overview
The project with the aim of gaining insights about the real estate sales in the state of Connecticut between the years 2001 and 2022. For the analysis, this project uses Python and its libraries of pandas, numpy, matplotlib and seaborn. Before the start of the project, several questions were defined to have a clear roadmap:

### Project Questions:
* How did the real estate sales fluctuate throughout the years?
* Does the residential type have an impact on the amount of sales in any way?
  * Which residential types are in more demand?
* Which cities are the most and least expensive in terms of average sale amount?
* Which cities are overvalued and undervalued the most, given their respective sales amount and corresponding sales ratios?

## Dataset & Methods
---

### Dataset
The dataset was taken from the official website of [Data.gov](https://catalog.data.gov/dataset/real-estate-sales-2001-2018). It is normally collected by the Office of Policy and Management. The overall dataset contains general information about the real estate sales in the state of Connecticut. To have a general understanding of the nature of the data, here is a brief explanation of all columns:

|    column name     | description |
| -----------------  |-------|
|Serial Number		   |Unique id for each sale|
|List Year           |The year when a residential type was listed|
|Date Recorded	     |The date when a residential type was sold|
|Town	               |The name of the town where a given property is located at|
|Address	           |The address of the property|
|Assessed Value	     |The assessed value of the property|
|Sale Amount	       |The amount the property was saled|
|Sales Ratio         |A metric to determine whether a property is overvalued or not. It could be calculated by dividing Assessed value by the Sale amount|
|Property Type	     |e.g., Residential, Commercial|
|Residential Type	   |e.g., Single family, Two family, Condo|
|Non Use Code	       |Additional explanation if there is an unexpected difference between the assessed and market value of a property|
|Assessor Remarks    |Additional remarks made by assessor about the property which is not mentioned explicitly in the dataset at first|
|OPM remarks	       |Additional remarks of the Office of Policy and Management of Connecticut|
|Location	           |The exact location of the property given as latitude and longitude|
---

### Methods & Steps
Below are the general steps taken for the data analysis. A few example codes are given to have an idea of what has been done. To look at the full codes, you can refer to the [notebook](https://github.com/azizbarank/California-Infectious-Diseases-Analysis/blob/main/california_disease_analysis.ipynb).
1. Importing the necessary packages:
```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline
```

2. First quick glance at the dataset:
```python
df.head()
df.columns
df.shape
df.info()
df.value_counts()
```

3. Data cleaning:
   * Dropping both unuseful columns and the ones with high NaN values
     ```python
      df.isna().sum()
      df = df.drop(axis=1, columns=['Non Use Code',
                              'Assessor Remarks',
                              'OPM remarks',
                              'Location'
                              ])
      ```
   * Renaming the "Date Recorded" column and change its data type from object to date
     ```python
      df.rename(columns={'Date Recorded': 'Date'}, inplace=True)
      df.Date = pd.to_datetime(df.Date, format='%m/%d/%Y')
      ```

4. Gaining Insights:
   ##### - The fluctuation of real estate sales between 2001-2022 (*line plot + scatterplot*)
   ##### - The comparison of residential types in terms of amount of sales (*bar plot*)
   ##### - The most and least expensive cities in terms of average amount of sales (*bar plot*)
   ##### - The over and undervalued cities in terms of sales ratio (*bar plot*)
   
```python
   df['Year'] = df['Date'].dt.year
   df['Month'] = df['Date'].dt.month
   df['Month Name'] = df['Date'].dt.strftime('%B')

   yearly_sales = df.groupby('Year')['Sale Amount'].sum().reset_index()

   # Plot sales by year using a scatter plot with enhanced details
   plt.figure(figsize=(12, 6))
   sns.scatterplot(data=yearly_sales, x='Year', y='Sale Amount', s=100, color='blue', edgecolor='black', marker='o')
   .
   .
   residential_summary = df.groupby('Residential Type')['Sale Amount'].agg(['mean', 'median', 'sum', 'count']).reset_index()

   # Visualize average sales using a colorful bar plot
   plt.figure(figsize=(12, 6))
   sns.barplot(data=residential_summary, x='Residential Type', y='mean', palette='viridis', edgecolor='black')
   .
   .

   # Group by Town and calculate the average sale amount
   city_avg_sales = df.groupby('Town')['Sale Amount'].mean().reset_index()

   # Sort the towns by average sale amount in descending order
   sorted_cities = city_avg_sales.sort_values(by='Sale Amount', ascending=False).reset_index(drop=True)

   # Select the top 10 most expensive and bottom 10 least expensive cities
   top_10_cities = sorted_cities.head(10)
   bottom_10_cities = sorted_cities.tail(10)
   .
   .

   city_sales_ratio = df.groupby('Town')['Sales Ratio'].mean().reset_index()
   # Add a column to determine whether the city is overvalued or undervalued
   city_sales_ratio['Valuation'] = city_sales_ratio['Sales Ratio'].apply(lambda x: 'Overvalued' if x > 1 else 'Undervalued')

   # Sort cities by their average sales ratio
   sorted_cities_ratio = city_sales_ratio.sort_values(by='Sales Ratio', ascending=False).reset_index(drop=True)
   .
   .
```
   ---
## Results (Project Questions)
1. How did the real estate sales fluctuate throughout the years?
![The amount of sales over years](https://github.com/azizbarank/Connecticut-Real-Estate-Analysis/blob/main/images/sales_year.png)

According to the plot, it seems that between 1999-2005 there was a sharp increase, which was followed by a slightly less sharp decrease until 2011. After that until 2021, there seems to be a high increase as well, reaching all time top during 2021. In the last two years (2022-2023) it seems that sales once again was decreased.

In addition to this, we can also look at in a bot more detail by considering the months so that we can have an idea of which part of a year the properties tend to be sold more. Since there would be a lot months if we include all the years, for simplicity, we can only look at the last four years.

![The amount of sales over months](https://github.com/azizbarank/Connecticut-Real-Estate-Analysis/blob/main/images/sales_month.png)

It seems that properties tend to be sold much more frequently during summer, in the order of August, July and June. Furthermore, this seems to be a common pattern across all years.

2. Does the residential type have an impact on the amount of sales in any way?
   * Which residential types are in more demand
![Residential Type](https://github.com/azizbarank/Connecticut-Real-Estate-Analysis/blob/main/images/resident_sale.png)

It seems that among the resident types, the properties for single families are in demand the most.

3. Which cities are the most and least expensive in terms of average sale amount?
![Expenses](https://github.com/azizbarank/Connecticut-Real-Estate-Analysis/blob/main/images/expenses.png)
Willington, Greenwich and Darien are the most expensive 3 cities, while Stafford, Torrington and Windham are the cities with the lowest prices.

4. Which cities are overvalued and undervalued the most, given their respective sales amount and corresponding sales ratios?
![Overvalued](https://github.com/azizbarank/Connecticut-Real-Estate-Analysis/blob/main/images/overvalue.png)
Salisbury, Bethany and Beacon Falls are the most overvalued three cities, while Warren, Colchester and Willington are the most undervalued ones.
