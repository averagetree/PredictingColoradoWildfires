# Predicting Colorado Wildfires

## Overview




## Data Sources





## Exploratory Data Analysis

Get the Lat/Long for weather location

RIFLE GARFIELD CO AIRPORT, CO US           39.5227° N, 107.7180° W

CRAIG MOFFAT CO AIRPORT, CO US             40.495556 N, 107.525" W

ASPEN PITKIN CO AIRPORT SARDY FIELD, CO US 39.2197° N, 106.8646° W

LIMON, CO US                               39.2639° N, 103.6922° W

GEORGETOWN, CO US                          39.7061° N, 105.6975° W

MONTROSE REGIONAL AIRPORT, CO US           38.5002° N, 107.8992° W

LEROY 5 WSW, CO US                         40.5261° N, 102.9141° W

CROOK, CO US                               40.8589° N, 102.8010° W

GRAND LAKE 6 SSW, CO US                    40.2522° N, 105.8231° W

MEEKER AIRPORT, CO US                      40.0407° N, 107.8923° W

CORTEZ MONTEZUMA CO AIRPORT, CO US         37.3044° N, 108.6300° W

COLORADO SPRINGS MUNICIPAL AIRPORT, CO US  38.8017° N, 104.7026° W

DURANGO LA PLATA CO AIRPORT, CO US         37.1597° N, 107.7507° W

TRINIDAD AIRPORT, CO US                    37.1695° N, 104.5005° W

LEADVILLE LAKE CO AIRPORT, CO US           39.2239° N, 106.3146° W

Source: Google Search



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
```





## 









