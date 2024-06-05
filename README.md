
<p align="center"><b>Analysis of Taxi Travel Behavior in Chicago: Popular Destinations,
Prominent Companies, and the Impact of Weather Conditions</b></p>


The following project aims to analyze taxi travel behavior in the city of Chicago based on three main aspects:

- Neighborhoods
- Taxi companies
- The relationship between trip duration and weather conditions

  These are some of the libraries used in the project:

```python
import pandas as pd
import numpy as np
from matplotlib import pyplot as plt
import math as mt
import seaborn as sb
from scipy.stats import ttest_ind, levene
```

Also, for the analysis three different datasets were used:

- <b>company info</b>: It contains the names of the companies and the amount of trips per company between November 15th and 16th of 2017 ([Click Here](https://github.com/Natcol05/Taxi-Travels-in-Chicago/blob/c7bbf61ca37c6864e42e8207c3ed718dafef790f/moved_project_sql_result_01%20(1).csv)) 

- <b>trips</b>: It shows the neighborhoods in Chicago where the trips ended and the average number of trips that concluded in each neighborhood in November 2017([Click Here](https://github.com/Natcol05/Taxi-Travels-in-Chicago/blob/c7bbf61ca37c6864e42e8207c3ed718dafef790f/moved_project_sql_result_04.csv))

- <b>df</b>: This is a dataframe built using a small web scraping technique. It contains data about trips from the Loop to Chicago O'Hare International Airport, including the start date, weather conditions, and trip duration. Requests, Beautiful Soup, and Pandas were used for extracting information, while SQL was utilized for the aggregation of the information into one dataframe ([Click Here](https://github.com/Natcol05/Taxi-Travels-in-Chicago/blob/c7bbf61ca37c6864e42e8207c3ed718dafef790f/moved_project_sql_result_07.csv))

Here, I used Requests, Beautiful Soup, and Pandas to extract and create a dataframe named weather_records with the weather information for Chicago in 2017: 
```python

import requests
from bs4 import BeautifulSoup
import pandas as pd
URL='https://practicum-content.s3.us-west-1.amazonaws.com/data-analyst-eng/moved_chicago_weather_2017.html'
req = requests.get(URL)
soup = BeautifulSoup(req.text, 'lxml')
table = soup.find('table', attrs={"id": "weather_records"})
heading_table=[]
for row in table.find_all('th'):
    heading_table.append(row.text)   
content=[]
for row in table.find_all('tr'):
    if not row.find_all('th'):
        content.append([element.text for element in row.find_all('td')])
weather_records = pd.DataFrame(content, columns = heading_table)
print(weather_records)
```

Additionally, I used SQL to combine the 'trip' dataframe with the dataframe named 'weather_records'. Then, I created a column classifying the weather as "Good" or "Bad" based on specific conditions:
```SQL
SELECT
    start_ts,
    T.weather_conditions,
    duration_seconds
FROM 
    trips
INNER JOIN (
    SELECT
        ts,
        CASE
            WHEN description LIKE '%rain%' OR description LIKE '%storm%' THEN 'Bad'
            ELSE 'Good'
        END AS weather_conditions
    FROM 
        weather_records          
) T on T.ts = trips.start_ts
WHERE 
    pickup_location_id = 50 AND dropoff_location_id = 63 AND EXTRACT (DOW from trips.start_ts) = 6
ORDER BY trip_id
```
<b>After the analysis some of spects to highlight are:</b>

1. Loop and River North are the neighborhoods with the highest concentration of destination trips, suggesting that they are quite popular areas in the city. However, Streeterville and West Loop, while having a considerably lower number of destination trips compared to the other 6 neighborhoods, are also noteworthy. It could be said that there is a significant concentration among these 4 mentioned neighborhoods compared to the others.

<p align="center">
  <img src="https://github.com/Natcol05/Taxi-Travels-in-Chicago/blob/610975a7883f3ad5a887e8df3e0dcd51b14ebba2/Graphics/Most_popular_neighborhoods.png" alt="Sample Image">
</p>
    
2. The analysis reveals that Flash Cab stands out as the most popular company among the top 10, with a considerable concentration of trips compared to the others. While the remaining companies exhibit a downward trend, the decline appears to be more or less uniform across the board. This underscores the significant dominance of Flash Cab in the market.

<p align="center">
  <img src="https://github.com/Natcol05/Taxi-Travels-in-Chicago/blob/610975a7883f3ad5a887e8df3e0dcd51b14ebba2/Graphics/Most_popular_companies.png" alt="Sample Image">
</p>

3. To assess whether the average duration of trips from the Loop to O'Hare International Airport differs on rainy Saturdays, we conducted a hypothesis test with the following hypotheses:

<b>Null Hypothesis:</b> The average duration of trips is not different for rainy and non-rainy Saturdays.

<b>Alternative Hypothesis:</b> The average duration of trips is different for rainy and non-rainy Saturdays.

```SQL
t_stat, p_value = ttest_ind(rainy_trips['duration_seconds'], non_rainy_trips['duration_seconds'], equal_var=True)
alpha = 0.05

print("Average trip duration on rainy days:", average_duration_rainy_days)
print("Average trip duration on non-rainy days:", average_duration_non_rainy_days)
print("P-value of the t-test:", p_value)

if p_value < alpha:
    print("The difference in average trip duration between rainy and non-rainy days is significant, \nso the null hypothesis is rejected.")
else:
    print("There is not enough evidence to conclude that the difference in average trip duration between rainy and non-rainy days is significant, \nso the null hypothesis is not rejected.")
```
    
It's noteworthy that prior to hypothesis testing, we assessed the variance of the two datasets, set the equal_var parameter accordingly, and performed Levene's test. Further details on this procedure can be found in the complete project documentation

**Main Conclusions:**

1. **About the Most Popular Neighborhoods:** It can be observed that certain areas of the city concentrate a significant number of trips, presumably neighborhoods with attractions that become primary destinations. For example, according to internet searches, the Loop area in Chicago is the financial district, hosting multiple iconic landmarks, restaurants, bars, and theaters, making it the logical main destination. This explains the presence of extreme values in this dataset, as other neighborhoods not among the main destinations may primarily consist of residential areas, resulting in broader options for trip destinations and lower data concentration. However, this doesn't imply that there aren't residential neighborhoods with higher trip volumes than others, but not with as high a concentration as the top 4 destination neighborhoods.

2. **About the Most Popular Companies:** Flash Cab stands out as the leading taxi company compared to others, although there are also extreme values. However, after this company, the decrease in trips per company is not as drastic.

3. **Regarding the Hypothesis:** A difference was found in the average trip duration on rainy Saturdays compared to non-rainy Saturdays, which could also be attributed to increased traffic due to weather conditions.
