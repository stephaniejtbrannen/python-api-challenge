
# Analysis


1. The temperature rises as the cities get closer to the equator.
2. The humidity rises as the cities get closer to the equator.
3. Wind Speed and Cloudiness are not related to the distance the city is from the equator


```python
# Dependencies
import matplotlib.pyplot as plt
from citipy import citipy
import openweathermapy.core as owm
import pandas as pd
import numpy as np
from random import randint
import urllib as u

# import api_key from config file
from config import api_key
```


```python
# Create a settings object with your API key and preferred units
settings = {"units": "imperial", "appid": api_key}
```


```python
#Generate City list from CitiPy library

cities = []
temp_data = []
counter = 0
print('Beginning Data Retrieval')
print('------------------------------------')

#Create list of 500 cities that have weather data
while (len(cities)<501):
    lat = randint(-90,90)
    lon = randint(-180,180)
    city = citipy.nearest_city(lat,lon)
    if city.city_name not in cities:
        try:
            city_data=owm.get_current(city.city_name, **settings)
            temp_data.append(city_data)
            cities.append(city.city_name)
            url = owm.BASE_URL
            query = u.parse.urlencode({'appid' : api_key, 'q' : city.city_name})
            counter +=1
            print(f'Processing Record {counter} | {city.city_name}')
            print (url + 'weather?' + query)
        except Exception as e:
            pass


print('------------------------------------')
print('Data Retrieval Complete')
print('------------------------------------')
```


```python
#Create Dataframe for data used in analysis
summary = ["clouds.all", "sys.country", "dt", "main.humidity", "coord.lat", "coord.lon", "main.temp_max", "wind.speed"]
column_names = ["Cloudiness", "Country", "Date", "Humidity", "Lat", "Lng", "Max Temp", "Wind Speed"]
data = [response(*summary) for response in temp_data]


temp_data = pd.DataFrame(data, index=cities, columns=column_names)
temp_data.head()
```


```python
#Create scatter plot for Latitude vs Temperature
import datetime
from matplotlib.dates import DateFormatter
temp = temp_data["Max Temp"]
latitude = temp_data["Lat"]
weather_date = datetime.datetime.today().strftime('%m/%d/%Y')
plt.scatter(latitude, temp, edgecolor = "black", linewidths=1, marker = 'o', alpha=0.8)
plt.title(f'City Latitude vs Max Temperature ({weather_date})')
plt.ylabel("Max Temperature (F)")
plt.xlabel("Latitude")
plt.grid(True)
plt.show
```


```python
#Create Scatter plot for City Latitude vs City
import datetime
from matplotlib.dates import DateFormatter
humidity = temp_data["Humidity"]
latitude = temp_data["Lat"]
weather_date = datetime.datetime.today().strftime('%m/%d/%Y')
plt.scatter(latitude, humidity, edgecolor = "black", linewidths=1, marker = 'o', alpha=0.8)
plt.title(f'City Latitude vs Humidity ({weather_date})')
plt.ylabel("Humidity (%)")
plt.xlabel("Latitude")
plt.grid(True)
plt.show
```


```python
#Create seaborn scatter plot for city latitude vs. cloudiness
import seaborn as sns
import datetime
from matplotlib.dates import DateFormatter

sns.set()
cloud = temp_data["Cloudiness"]
latitude = temp_data["Lat"]
weather_date = datetime.datetime.today().strftime('%m/%d/%Y')
plt.scatter(latitude, cloud, edgecolor = "black", linewidths=1, marker = 'o', alpha=0.8)
plt.title(f'City Latitude vs Cloudiness ({weather_date})')
plt.ylabel("Cloudiness (%)")
plt.xlabel("Latitude")
plt.show
```


```python
#create seaborn scatter plot for city latitude vs. windspeed
import datetime
from matplotlib.dates import DateFormatter
wind = temp_data["Wind Speed"]
latitude = temp_data["Lat"]
weather_date = datetime.datetime.today().strftime('%m/%d/%Y')
print(weather_date)
plt.scatter(latitude, wind, edgecolor = "black", linewidths=1, marker = 'o', alpha=0.8)
plt.title(f'City Latitude vs Wind Speed ({weather_date})')
plt.ylabel("Wind Speed (mph)")
plt.xlabel("Latitude")
plt.grid(True)
plt.show
```
