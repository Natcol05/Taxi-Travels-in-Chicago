
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

- <b>df</b>: df: This is a dataframe built using a small web scraping technique. It contains data about trips from the Loop to Chicago O'Hare International Airport, including the start date, weather conditions, and trip duration. Requests, Beautiful Soup, and Pandas were used for extracting information, while SQL was utilized for the aggregation of the information into one dataframe ([Click Here](https://github.com/Natcol05/Taxi-Travels-in-Chicago/blob/c7bbf61ca37c6864e42e8207c3ed718dafef790f/moved_project_sql_result_07.csv))

Here, I used Requests, Beautiful Soup, and Pandas to extract weather information for Chicago in 2017: 
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
