

```python
# sns.set_style("whitegrid")
# plt.figure()
# axes.set_xticklabels(list(range(min_lat, max_lat, 30)))
# ax = sns.stripplot(x="Latitude", y="Temperature (F)", data=temp_vs_lat_df)

# axes.set_xlim(left=-54, right=80, auto=True)
# plt.show()
```


```python
#dependencies
import requests
import json
import datetime
import pandas as pd
import random
import numpy as np
from citipy import citipy
import time
import seaborn as sns
import matplotlib.pyplot as plt
import matplotlib.patches as patches
from matplotlib.patches import Rectangle
import matplotlib.ticker as ticker
from matplotlib.dates import DateFormatter
```


```python
counter = 0
city_names = []
while counter < 500:
    lat = random.randrange(-90,90)
    long = random.randrange(-180, 180)
    city = citipy.nearest_city(lat, long)
    if city.city_name not in city_names: 
        city_names.append({"city":city.city_name, "country_code":city.country_code})
        counter+= 1
    
len(city_names)
```




    500




```python
#declare lists to for scatterplots
temp_vs_lat = []
humidity_vs_lat = []
cloudiness_vs_lat = []
wind_speed_vs_lat = []

#variable to store the data
retrieved_data_log = []
```


```python
api_key = "5a496d19cea198d1a855c13dbc0b8529"
units = "imperial"
counter = 0
#lakatoro, vu
for c in city_names:
    countryCode = c["country_code"]
    city = c["city"]
    target_url = "http://api.openweathermap.org/data/2.5/weather?q={%s},{%s}&mode=json&APPID=%s&units=%s" % (city, countryCode, api_key, units)
    city_weather = requests.get(target_url).json()
    if city_weather["cod"] != "404":
        retrieved_data_log.append({"city": city, "country": countryCode, "url": target_url}) #"city id": city_weather["id"], 
        temp_vs_lat.append({"Temperature (F)":city_weather["main"]["temp_max"],"Latitude":city_weather["coord"]["lat"]})
        humidity_vs_lat.append({"Humidity (%)":city_weather["main"]["humidity"],"Latitude":city_weather["coord"]["lat"]})
        cloudiness_vs_lat.append({"Cloudiness (%)":city_weather["clouds"]["all"],"Latitude":city_weather["coord"]["lat"]})
        wind_speed_vs_lat.append({"Wind Speed (mph)":city_weather["wind"]["speed"], "Latitude":city_weather["coord"]["lat"]})
        time.sleep(3)
        counter += 1

temp_vs_lat_df = pd.DataFrame(temp_vs_lat)
```


```python
#create a dataframe from the extracted data
temp_vs_lat_df = pd.DataFrame(temp_vs_lat)

#define the colors for ranges in temperature
colors = []
for i in temp_vs_lat_df["Temperature (F)"]:
        if i <= 50:
            colors.append("#f47142")
        elif i <= 70:
            colors.append("#c44c02")
        elif i <= 90:
            colors.append("#c40303")
        else:
            colors.append("#d10445")

#label the axis and create a title for the scatter plot
plt.title("Temperature (F) vs Latitude")
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")


#add an arrow to the scatter plot to show that x=0 is the equator
plt.annotate('Equator',
            xy=(0, 20), xycoords='data',
            xytext=(15, 33), textcoords='offset points',
            size=15,
            arrowprops=dict(arrowstyle="fancy",
                            fc="0.1", ec="none",
                            connectionstyle="angle3,angleA=0,angleB=-90"))

plt.text(85, 60, "The scatter plot shows temperatures \nrising as we approach the equator.")


#define the data for the scattor plot
plt.scatter(x=temp_vs_lat_df["Latitude"], y=temp_vs_lat_df["Temperature (F)"], c=colors, alpha=0.5)

#show the scatter plot
plt.show()
```


![png](output_5_0.png)



```python
#create a dataframe for the extracted data
humidity_vs_lat_df = pd.DataFrame(humidity_vs_lat)

#label the axis and create a title for the scatter plot
plt.title("Humidity % vs Latitude")
plt.xlabel("Latitude")
plt.ylabel("Humidity %")

#add an arrow to the scatter plot to show that x=0 is the equator
plt.annotate('Equator',
            xy=(0, 10), xycoords='data',
            xytext=(10, 43), textcoords='offset points',
            size=15,
            arrowprops=dict(arrowstyle="fancy",
                            fc="0.1", ec="none",
                            connectionstyle="angle3,angleA=0,angleB=-90"))

plt.text(85, 60, "The scatter plot shows there is a weak correlation \nbetween humidity and the temperatures we approach \nthe equator since warm air can hold more moisture \nthan cool air.")


#define the data for the scattor plot
plt.scatter(x=humidity_vs_lat_df["Latitude"], y=humidity_vs_lat_df["Humidity (%)"], c=colors, alpha=0.5)

#show the scatter plot
plt.show()
```


![png](output_6_0.png)



```python
#create a dataframe for the extracted data
cloudiness_vs_lat_df = pd.DataFrame(cloudiness_vs_lat)

#label the axis and create a title for the scatter plot
plt.title("Cloudiness % vs Latitude")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness %")

#add an arrow to the scatter plot to show that x=0 is the equator
plt.annotate('Equator',
            xy=(0, 3), xycoords='data',
            xytext=(20, 23), textcoords='offset points',
            size=15,
            arrowprops=dict(arrowstyle="fancy",
                            fc="0.1", ec="none",
                            connectionstyle="angle3,angleA=0,angleB=-90"))

plt.text(85, 40, "The scatter plot shows there is no correlation between the \ncloudiness and the distance to the equator.")


#define the data for the scattor plot
plt.scatter(x=cloudiness_vs_lat_df["Latitude"], y=cloudiness_vs_lat_df["Cloudiness (%)"], c=colors, alpha=0.5)

#show the scatter plot
plt.show()
```


![png](output_7_0.png)



```python
#Wind Speed (mph) (F) vs latitude
wind_speed_vs_lat_df = pd.DataFrame(wind_speed_vs_lat)

#label the axis and create a title for the scatter plot
plt.title("Wind Speed (mph) vs Latitude")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (mph)")

#add an arrow to the scatter plot to show that x=0 is the equator
plt.annotate('Equator',
            xy=(0, 0), xycoords='data',
            xytext=(30, 13), textcoords='offset points',
            size=15,
            arrowprops=dict(arrowstyle="fancy",
                            fc="0.1", ec="none",
                            connectionstyle="angle3,angleA=0,angleB=-90"))

plt.text(85, 10, "The scatter plot shows there is no correlation between the \nwind speed and the distance to the equator.")


#define the data for the scattor plot
plt.scatter(x=wind_speed_vs_lat_df["Latitude"], y=wind_speed_vs_lat_df["Wind Speed (mph)"], c=colors, alpha=0.5)

#show the scatter plot
plt.show()
```


![png](output_8_0.png)



```python
retrieved_data_log_df = pd.DataFrame(retrieved_data_log).reset_index()
retrieved_data_log_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>city</th>
      <th>country</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>clyde river</td>
      <td>ca</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>mehamn</td>
      <td>no</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>hengyang</td>
      <td>cn</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>clyde river</td>
      <td>ca</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>mehamn</td>
      <td>no</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
  </tbody>
</table>
</div>




```python
#export the weather url into a csv file
file_name = "weather_data_log.csv"
retrieved_data_log_df[["city", "country","url"]].to_csv(file_name, encoding='utf-8')
```


```python

```
