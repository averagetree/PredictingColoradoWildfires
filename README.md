# Predicting Colorado Wildfires

## Overview

The overall goal for this analysis is to assess the weather conditions that are correlated with wildfire occurrences in the state of Colorado and attempt to make predictions for future wildfires.  

## Data Sources

Two major data sources are used for this analysis. The first data set consists of wildfire data from NOAA.

```
cnx = sqlite3.connect('FPA_FOD_20210617.sqlite')

cursor = cnx.cursor()
cursor.execute("SELECT name FROM sqlite_master WHERE type='table';")
print(cursor. fetchall())

df = pd.read_sql_query("SELECT * FROM Fires", cnx)

```
Colorado is the sole focus for this analysis, so the data set is trimmed down to just the state of Colorado. 

```
df_co = df.loc[df['STATE'] == "CO"]

```

The second data set consists of weather data captured from various weather stations throughout the state of Colorado.

```
df_weather = pd.read_csv('3090639.csv')

```

It was a challenge to find weather data that was complete at a granular level, as the data was either too general or just missing key values.

## Exploratory Data Analysis

Contents of the wildfire data set.

```
df_co.info()

<class 'pandas.core.frame.DataFrame'>
Int64Index: 61226 entries, 47 to 2166726
Data columns (total 37 columns):
 #   Column                         Non-Null Count  Dtype  
---  ------                         --------------  -----  
 0   FOD_ID                         61226 non-null  int64  
 1   FPA_ID                         61226 non-null  object 
 2   SOURCE_SYSTEM_TYPE             61226 non-null  object 
 3   SOURCE_SYSTEM                  61226 non-null  object 
 4   NWCG_REPORTING_AGENCY          61226 non-null  object 
 5   NWCG_REPORTING_UNIT_ID         61226 non-null  object 
 6   NWCG_REPORTING_UNIT_NAME       61226 non-null  object 
 7   SOURCE_REPORTING_UNIT          61226 non-null  object 
 8   SOURCE_REPORTING_UNIT_NAME     61226 non-null  object 
 9   LOCAL_FIRE_REPORT_ID           15274 non-null  object 
 10  LOCAL_INCIDENT_ID              37902 non-null  object 
 11  FIRE_CODE                      21806 non-null  object 
 12  FIRE_NAME                      54081 non-null  object 
 13  ICS_209_PLUS_INCIDENT_JOIN_ID  713 non-null    object 
 14  ICS_209_PLUS_COMPLEX_JOIN_ID   1 non-null      object 
 15  MTBS_ID                        267 non-null    object 
 16  MTBS_FIRE_NAME                 267 non-null    object 
 17  COMPLEX_NAME                   53 non-null     object 
 18  FIRE_YEAR                      61226 non-null  int64  
 19  DISCOVERY_DATE                 61226 non-null  object 
 20  DISCOVERY_DOY                  61226 non-null  int64  
 21  DISCOVERY_TIME                 56517 non-null  float64
 22  NWCG_CAUSE_CLASSIFICATION      61226 non-null  object 
 23  NWCG_GENERAL_CAUSE             61226 non-null  object 
 24  NWCG_CAUSE_AGE_CATEGORY        367 non-null    object 
 25  CONT_DATE                      48710 non-null  object 
 26  CONT_DOY                       48710 non-null  float64
 27  CONT_TIME                      48518 non-null  float64
 28  FIRE_SIZE                      61226 non-null  float64
 29  FIRE_SIZE_CLASS                61226 non-null  object 
 30  LATITUDE                       61226 non-null  float64
 31  LONGITUDE                      61226 non-null  float64
 32  OWNER_DESCR                    61226 non-null  object 
 33  STATE                          61226 non-null  object 
 34  COUNTY                         45171 non-null  object 
 35  FIPS_CODE                      45171 non-null  object 
 36  FIPS_NAME                      45171 non-null  object 
dtypes: float64(6), int64(3), object(28)
memory usage: 17.8+ MB

```

Drop unused values from the wildfire data set.

```
# Drop unused columns
df_co.drop(columns=['FPA_ID', 'SOURCE_SYSTEM_TYPE', 'SOURCE_SYSTEM', 'NWCG_REPORTING_AGENCY', 'NWCG_REPORTING_AGENCY', 
                    'NWCG_REPORTING_UNIT_ID','NWCG_REPORTING_UNIT_NAME','LOCAL_FIRE_REPORT_ID','LOCAL_INCIDENT_ID',
                    'SOURCE_REPORTING_UNIT', 'ICS_209_PLUS_INCIDENT_JOIN_ID', 'ICS_209_PLUS_INCIDENT_JOIN_ID', 'MTBS_ID',
                    'MTBS_FIRE_NAME', 'COMPLEX_NAME', 'OWNER_DESCR', 'COUNTY', 'STATE', 'FIPS_CODE', 'FIPS_NAME',
                    'ICS_209_PLUS_COMPLEX_JOIN_ID', 'SOURCE_REPORTING_UNIT_NAME', 'FIRE_CODE', 'FIRE_NAME',
                    'NWCG_CAUSE_AGE_CATEGORY'], axis=1, inplace=True)

```

Contents of the Weather data set.

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 131269 entries, 0 to 131268
Data columns (total 45 columns):
 #   Column   Non-Null Count   Dtype  
---  ------   --------------   -----  
 0   STATION  131269 non-null  object 
 1   NAME     131269 non-null  object 
 2   DATE     131269 non-null  object 
 3   AWND     87071 non-null   float64
 4   DAWM     2 non-null       float64
 5   FMTM     36212 non-null   float64
 6   MDWM     2 non-null       float64
 7   PGTM     50957 non-null   float64
 8   PRCP     126477 non-null  float64
 9   TAVG     31231 non-null   float64
 10  TMAX     130897 non-null  float64
 11  TMIN     130888 non-null  float64
 12  TOBS     42475 non-null   float64
 13  WDF1     516 non-null     float64
 14  WDF2     79937 non-null   float64
 15  WDF5     79837 non-null   float64
 16  WDFG     3433 non-null    float64
 17  WDMV     1166 non-null    float64
 18  WSF1     516 non-null     float64
 19  WSF2     79937 non-null   float64
 20  WSF5     79882 non-null   float64
 21  WSFG     3461 non-null    float64
 22  WT01     16845 non-null   float64
 23  WT02     3194 non-null    float64
 24  WT03     9752 non-null    float64
 25  WT04     176 non-null     float64
 26  WT05     765 non-null     float64
 27  WT06     174 non-null     float64
 28  WT07     193 non-null     float64
 29  WT08     6148 non-null    float64
 30  WT09     312 non-null     float64
 31  WT10     11 non-null      float64
 32  WT11     491 non-null     float64
 33  WT13     2166 non-null    float64
 34  WT14     187 non-null     float64
 35  WT15     71 non-null      float64
 36  WT16     4546 non-null    float64
 37  WT17     54 non-null      float64
 38  WT18     2196 non-null    float64
 39  WT19     138 non-null     float64
 40  WT21     24 non-null      float64
 41  WT22     518 non-null     float64
 42  WV01     8 non-null       float64
 43  WV03     260 non-null     float64
 44  WV20     7 non-null       float64
dtypes: float64(42), object(3)
memory usage: 45.1+ MB

```
Drop unused columns from the weather data set. Most were empty or contained irrelavant ID values.

```
# Drop unused columns
df_weather.drop(columns=['WT01','WT02','WT03','WT04','WT05','WT06','WT07','WT08','WT09','WT10','WT11',
                        'WT13','WT14','WT15','WT16','WT17','WT18','WT19','WT21','WT22','WV01','WT03',
                        'WV20', 'WSF1', 'MDWM', 'DAWM', 'TOBS', 'WV03'], axis=1, inplace=True)

```

Further cleanup/format changes to the data.

```
df_weather = df_weather.fillna(0)
df_weather['DATE'] = pd.to_datetime(df_weather.DATE)

```

```
Get the Lat/Long for weather location

RIFLE GARFIELD CO AIRPORT, CO US           39.5227?? N, 107.7180?? W

CRAIG MOFFAT CO AIRPORT, CO US             40.495556 N, 107.525" W

ASPEN PITKIN CO AIRPORT SARDY FIELD, CO US 39.2197?? N, 106.8646?? W

LIMON, CO US                               39.2639?? N, 103.6922?? W

GEORGETOWN, CO US                          39.7061?? N, 105.6975?? W

MONTROSE REGIONAL AIRPORT, CO US           38.5002?? N, 107.8992?? W

LEROY 5 WSW, CO US                         40.5261?? N, 102.9141?? W

CROOK, CO US                               40.8589?? N, 102.8010?? W

GRAND LAKE 6 SSW, CO US                    40.2522?? N, 105.8231?? W

MEEKER AIRPORT, CO US                      40.0407?? N, 107.8923?? W

CORTEZ MONTEZUMA CO AIRPORT, CO US         37.3044?? N, 108.6300?? W

COLORADO SPRINGS MUNICIPAL AIRPORT, CO US  38.8017?? N, 104.7026?? W

DURANGO LA PLATA CO AIRPORT, CO US         37.1597?? N, 107.7507?? W

TRINIDAD AIRPORT, CO US                    37.1695?? N, 104.5005?? W

LEADVILLE LAKE CO AIRPORT, CO US           39.2239?? N, 106.3146?? W

Source: Google Search
```

A key component of this analysis requires both the weather and wildfire datasets to be linked together. This can be done by identifying the nearest weather station to each wildfire instance. The following algorithm finds the nearest lat/long from a list of lat/long values from a given lat/long value. Source from StackOverflow. 

```
from math import cos, asin, sqrt

def distance(lat1, lon1, lat2, lon2):
    p = 0.017453292519943295
    hav = 0.5 - cos((lat2-lat1)*p)/2 + cos(lat1*p)*cos(lat2*p) * (1-cos((lon2-lon1)*p)) / 2
    return 12742 * asin(sqrt(hav))

def closest(data, v):
    return min(data, key=lambda p: distance(v['lat'],v['long'],p['lat'],p['long']))

def get_nearest_station(lat, long):
    v = {'lat': lat, 'long': long}
    
    lat_key_list = list(lat_vals.keys())
    lat_value_list = list(lat_vals.values())
    
    nearestStationLatLong = closest(stationLatLongList, v)
    nearestStation = lat_key_list[lat_value_list.index(nearestStationLatLong['lat'])]
    return nearestStation

df_co['NEAREST_STATION'] = df_co.apply(lambda row: get_nearest_station(row.LATITUDE, row.LONGITUDE), axis=1)

```
With the nearest station identified for each wildfire observation, the weather data set can be updated to reflect whether or not a wildfire occurred for a certain station on a certain day.

```
for i, row in df_co.iterrows():
    df_weather.loc[((df_weather.DATE == row.DISCOVERY_DATE) & (df_weather.NAME == row.NEAREST_STATION)), 'FIRE'] = 1

df_weather['FIRE'] = df_weather['FIRE'].fillna(0)
```

![output4](https://user-images.githubusercontent.com/75912501/196061129-167cd36c-1f77-42f7-b792-912b45535a77.png)


## Logistic Regression Model

```
# Create Logistic Regression model
lr_model =  LogisticRegression(solver='liblinear') 

# Fit the LR model with data 
lr_model.fit(x_train,y_train) 

# Create predictions
y_pred=lr_model.predict(x_test) 
y_pred 

```

Output:

Accuracy: 0.8062953257358767

Precision: 0.5859697386519945

Recall: 0.06572045664918236

![output5](https://user-images.githubusercontent.com/75912501/195901390-bd17ded8-87aa-4970-b0e2-ebb4b7eecb35.png)

Overall, the Logistic Regression model does a decent job at a 0.806 accuracy. I believe this number could be improved with a more complete data set.

## Random Forest Model

```
# creating a RF classifier
rfClass = RandomForestClassifier(n_estimators = 100) 
 
# Fit model
rfClass.fit(x_train, y_train)
 
# Perform Predictions
y_pred = rfClass.predict(x_test)

```
Output:

Accuracy:  0.8137607410567371

Precision:  0.5717054263565892

Recall:  0.2275532243134835

Overall, the Random Forest model does a better job than the Logistic Regression model at a 0.814 accuracy. As with the Logistic Regression Model, I believe this number could be improved with a more complete data set.

![output6](https://user-images.githubusercontent.com/75912501/195901405-e0c1bdcf-ec49-4ad0-8694-a3b211655fe9.png)



