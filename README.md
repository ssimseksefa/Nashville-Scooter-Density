# Project to address a major planning question for scooters

## Question:

 * What is the ideal density of available scooters to:
    * enable scooters to serve our transportation goals,
    * discourage scooters piling up on sidewalks,
    * keep it economically viable for companies to operate equitably in the city?
### Points to consider
* Major planning goal to reduce the number of people driving alone
* 3 rides (of 3 meters or more) per day per scooter is our baseline for ridership goals
* Original pilot program limited scooter density to 340 scooters per square mile
* Other cities have limited the number of scooters per block face


### The data
#### Shared Urban Mobility Device (SUMD) availability data for May, June, and July 2019  
```
    May data shape:  (20292503, 9)
   June data shape:  (28046095, 9)
   July data shape:  (25075445, 9)
   ```

**pubdatetime** - date and time that the device was polled  
**latitude** - latitude location of device when polled  
**longitude** - longitude location of device when polled  
**sumdid** - unique identifier for the device  
**sumdtype** - one of two types (powered or standard)  
**chargelevel** - battery charge level of the device when polled  
**sumdgroup** - type of device (scooter or bicycle)  
**costpermin** - the cost per minute of device use  
**companyname** - the company that owns the device

Per [ordinance](https://www.nashville.gov/Metro-Clerk/Legislative/Ordinances/Details/7d2cf076-b12c-4645-a118-b530577c5ee8/2015-2019/BL2018-1202.aspx): 
```
All permitted operators will first clean data before providing or reporting data to Metro. Data 
processing and cleaning shall include:  
 1. Removal of staff servicing and test trips
 2. Removal of trips below one minute
 3. Trip lengths are capped at 24 hours
 ```
Anecdotally, per metro ITS staff, some of these observations may still be in the data.

#### Shapefiles for Nashville Promise Zone 
SUMD devices are thought to be of particular use in the [Promise Zone](https://www.nashville.gov/Mayors-Office/Promise-Zone/Basics.aspx) to help mitigate the "last mile" problem that exists in connecting people where they live to public transportation.

Remember that you need to keep the shapefiles together. Even though you will point to the `.shp` file to create polygons, that file references other files in the `MDHA_Promise_Zones` folder to create the geoDataFrame.

## Analysis Outline:

The question is what is ideal density and goal is at least 3 ride per day for each scooter. We focused on METRO-defined Promise Zones and I decided to find how many scooters are available in small regions for density and I decided to find idle time (time interval of a scooter is available but not in use) to approximately find average ride per day.

### Data cleaning

Scooter availability data contains information of when and where a scooter is available. Although a scooter does not move, this file may have a lot of rows for different times. For example, a scooter is stable at a point for a month but there is 9000 rows for this scooter in this data. Some of the scooter sends location info for almost each 5 minutes. There is no need to keep so many rows if scooter does not move and it makes analysis inefficient. Hence I decided to keep only one row for each location including when the scooter available at that location first and last. I also realized that some of the scooter seems moving around the same location with very small location changes. The reason may be about GPS accuracy. GPS may send slightly different location info for the exact same location. Hence I decided a threshold distance to understand if a scooter moves in fact. After applying these cleaning steps it decreased from >20m rows to around 200k rows. 

We also have another data which shows each trip of a scooter. Each row contains trip route and when the trip starts and ends. I combined these two data and validated the availabilty with trips.

### Results

I created [interactive maps](http://data-playground.com/scooter_html/May_avg_idle_and_daily_trip.html) to show how many scooter is available and what is the average ride per day in each equally divided small regions.
