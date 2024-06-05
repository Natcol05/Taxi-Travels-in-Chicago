
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

- <b>df</b>: This is a dataframe built using a small web scraping technique. It contains data about trips from the Loop to Chicago O'Hare International Airport, including the start date, weather conditions, and trip duration. Both SQL and pandas were used: SQL for data extraction and pandas for analysis ([Click Here](https://github.com/Natcol05/Taxi-Travels-in-Chicago/blob/c7bbf61ca37c6864e42e8207c3ed718dafef790f/moved_project_sql_result_07.csv))
