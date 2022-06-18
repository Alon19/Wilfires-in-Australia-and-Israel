# Wilfires-in-Australia-and-Israel
## Data
The data was gathered from three main sources:

1. Australian Bureau of Meteorology (BoM) - from which we gathered information about rainfall and temperatures in seven major cities around Australia. Both tables contain data collected since 1884.
2. Israel Meteorological Institute - from which we extracted information about rainfall and temperatures in different stations around Israel. The tables contain samples of both precipitation and temperatures, from several stations scattered in Israel.
3. Nasa Firms - We sent a request to NASA to obtain satellite data of fires in Australia and
Israel, NASA was happy to fulfil the request.
This data was collected by NASA’s satellites, which follow wildfires all around the world, their progress and brightness (which gives indication about the heat radiating from the fire).
The tables contain more than 3,000,000 observations of fires in Australia and 19,000 fires in Israel, since 2010.
After inspecting every attribute of the data, relating to our research question, we have concluded that the attributes that are of most relevance to us are: brightness, frp (fire radiative power) and confidence in the NASA fires tables, the amount of rain in the rain tables, and the maximum temperature in the temperature’s tables.
Each sample of every table also contains a coordinate, indicating the location the sample was observed. This was used to join the data from the weather stations to the fire observations.


### Data_Dictionary

aus_temperature:
| variable | type | description |
| -------- | ---- | ---------- |
| date | date | Date |
| city_name | character | City Name |
| station_name | character | Actual site / weather station |
| station_number | double | Station number |
| temperature | double | Temperature in Celsius |
| accumulation_days | double | Accumulation days |
| quality | character | Certified quality or not |
| min.max | character | min or max temperature |
| latitude | double | Latitude |
| longitude | double | Longitude |
  
isr_temperature:
| variable | type | description |
| -------- | ---- | ---------- |
| station_name | character | Actual site / weather station |
| station_number | double | Station number |
| date | date | Date |
| max_temp | double | maximum temp |
| min_temp | double | minimum temp |
| min_temp_ground | double | Minimal temperature messured on ground |
| light_time | double | Light time |
| latitude | double | Latitude |
| longitude | double | Longitude |

  
aus_rainfall:
| variable | type | description |
| -------- | ---- | ---------- |
| date | date | Date |
| city_name | character | City Name |
| station_name | character | Station Name |
| station_number | character | Station Code |
| amount | double | rainfall in millimeters |
| period | double | how many days was is collected across |
| quality | character | Certified quality or not |
| lat | double | latitude |
| long | double | longitude |
  

isr_rainfall:
| variable | type | description |
| -------- | ---- | ---------- |
| station_name | character | Station Name |
| station_number | character | Station Code |
| date | date | Date |
| daily_amount | double | rainfall in millimeters |
| daily_rain_code | double | Daily Rain Code |


aus_fires:
| variable | type | description |
| -------- | ---- | ---------- |
| latitude | double | Center of 1km fire pixel but not necessarily the actual location of the fire as one or more fires can be detected within the 1km pixel. |
| longitude | double | Center of 1km fire pixel but not necessarily the actual location of the fire as one or more fires can be detected within the 1km pixel. |
| brightness | double | Channel 21/22 brightness temperature of the fire pixel measured in Kelvin. |
| scan | double | The algorithm produces 1km fire pixels but MODIS pixels get bigger toward the edge of scan. Scan and track reflect actual pixel size. |
| track | double | The algorithm produces 1km fire pixels but MODIS pixels get bigger toward the edge of scan. Scan and track reflect actual pixel size. |
| acq_date | date | Date of MODIS acquisition. |
| acq_time | character | Time of acquisition/overpass of the satellite (in UTC). |
| satellite | character | A = Aqua and T = Terra. |
| confidence | double | This value is based on a collection of intermediate algorithm quantities used in the detection process. It is intended to help users gauge the quality of individual hotspot/fire pixels. Confidence estimates range between 0 and 100% and are assigned one of the three fire classes (low-confidence fire, nominal-confidence fire, or high-confidence fire). |
| bright_t31 | double | Channel 31 brightness temperature of the fire pixel measured in Kelvin. |
| frp | double | Depicts the pixel-integrated fire radiative power in MW (megawatts). |
| day_night | character | D = Daytime, N = Nighttime |



isr_fires:
| variable | type | description |
| -------- | ---- | ---------- |
| latitude | double | Center of 1km fire pixel but not necessarily the actual location of the fire as one or more fires can be detected within the 1km pixel. |
| longitude | double | Center of 1km fire pixel but not necessarily the actual location of the fire as one or more fires can be detected within the 1km pixel. |
| brightness | double | Channel 21/22 brightness temperature of the fire pixel measured in Kelvin. |
| scan | double | The algorithm produces 1km fire pixels but MODIS pixels get bigger toward the edge of scan. Scan and track reflect actual pixel size. |
| track | double | The algorithm produces 1km fire pixels but MODIS pixels get bigger toward the edge of scan. Scan and track reflect actual pixel size. |
| acq_date | date | Date of MODIS acquisition. |
| acq_time | character | Time of acquisition/overpass of the satellite (in UTC). |
| satellite | character | A = Aqua and T = Terra. |
| confidence | double | This value is based on a collection of intermediate algorithm quantities used in the detection process. It is intended to help users gauge the quality of individual hotspot/fire pixels. Confidence estimates range between 0 and 100% and are assigned one of the three fire classes (low-confidence fire, nominal-confidence fire, or high-confidence fire). |
| bright_t31 | double | Channel 31 brightness temperature of the fire pixel measured in Kelvin. |
| frp | double | Depicts the pixel-integrated fire radiative power in MW (megawatts). |
| day_night | character | D = Daytime, N = Nighttime |
| instrument | character | satellite name |

#####################################################################################

## Analysis

### Step 1 - Data Preparation
1. Import and transform the raw data - data located at "./data/raw-data".
- Combine relevant data frames and remove unneccessary columns.
- Transform data types and sort by date.
2. Exploratory Data Analysis
- Examine each of the datasets
- Visualizations - Explore relationships between the different fields.
3. Join spatial data.
- Create function that returns a distance matrix between each fire location and  each weather station.
- Calculate the distance of each fire in Australia to the nearest rain and temperature station.
- - Calculate the distance of each fire in Israel to the nearest rain and temperature station.
- Add Temperature and Rainfall (7-day rolling sum) columns to each fire, measured by the closest weather station.
4. Export the Transformed Data to .csv Files.


#####################################################################################

### Step 2 - Predicting Confidence Level by Brightness and Fire Radiative Power

We wanted to use the confidence attribute as a Boolean indication, whether there was a real fire or not. However, the table containing the fires in Israel that we have received from NASA had missing values in the confidence column. We decided to fill the missing values based on the data we have from Australia, using a logistic-regression model. We wanted to decrease the level of uncertainty in our model, so we created a clear cut between fire and non-fire observations. We filtered the middle section of the confidence scale, which fall within [𝑎𝑣𝑔(𝑐𝑜𝑛𝑓)±𝑠𝑡𝑑(𝑐𝑜𝑛𝑓)]. Every sample outside said area got the value True (fire) or False (non-fire) respectively to the confidence level section.

1. Import Libraries, Data and Set Global Options - data located at "./data/brightness-frp-model".
2. Predict Fire’s Confidence Level by Brightness & FRP.
- Split Data into Train and Test.
- Run Logistic Regression on the Train data and Fit The Model.
- Predict the confidence level of each record in the Test data.
- Analyze Confusion Matrix’s metrics and ROC Curve of the model.
3. Test the model on Israel’s Fire data (Non-NA Confidence values).
- Filter for Non-NA values and preview the data.
- Predict the confidence level for each record in the data.
- Analyze Confusion Matrix’s metrics and ROC Curve of the model.
4. Fill Missing Values using Validated Model.
5. Export new data.


### Step 3 - Predicting Fires by Temperature and Rainfall

We wanted to try and predict the occurrence of a fire, given certain environmental variables.
We used the dataset from Australia and built a logistic-regression model, using rainfall, temperatures, and the distance from the nearest weather station.

1. Import Libraries, Data and Set Global Options - data located at "./data/temperature-rainfall-model".
2. Predict Fire’s Confidence Level by Temperature, Rainfall Amount and Distances to Nearest Stations.
- Split Data into Train and Test.
- Run Logistic Regression on the Train data and Fit The Model.
- Predict the confidence level of each record in the Test data.
- Analyze Confusion Matrix’s metrics and ROC Curve of the model.
3. Test the model on Israel’s Fire data (Non-NA Confidence values).
- Predict the confidence level of each record in the Test data.
- Analyze Confusion Matrix’s metrics and ROC Curve of the model.
