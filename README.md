# Seoul Bike Rental Prediction
![alt text](https://images.squarespace-cdn.com/content/v1/5bcfc8c07a1fbd730b2ba933/1556234236204-CHXB0WLKA5JBKD7AB6X7/IMG_1235.jpg?format=1500w)

## Problem
Currently Rental bikes are widely used for enhancement of mobility comfort and it is important to make the rental bike available and accessible to the public at the right time as it lessens the waiting time. The constant raise of users necessitates the existance of a bike rental system to predict the rental rate at each hour of the day.\

The dataset used in this project is obtained from [UC Irvine Machine Learning Repository - Seoul Bike Sharing Demand Dataset](https://archive.ics.uci.edu/ml/datasets/Seoul+Bike+Sharing+Demand#)\
It contains count of public bikes rented at each hour for every day during a full year from 12-2017 to 12-2018 in Seoul(Japan) Bike haring System with the corresponding Weather data and Holidays information.\

**Data fields:**
- ID - an ID for this instance
- Date - year-month-day
- Hour - Hour of he day
- Temperature - Temperature in Celsius
- Humidity - %
- Windspeed - m/s
- Visibility - 10m
- Dew point temperature - Celsius
- Solar radiation - MJ/m2
- Rainfall - mm
- Snowfall - cm
- Seasons - Winter, Spring, Summer, Autumn
- Holiday - Holiday/No holiday
- Functional Day - NoFunc(Non Functional Hours), Fun(Functional hours)
- y - Rented Bike count (Target), Count of bikes rented at each hour

**Note** This project was part of a Kaggle competition and came in the 13th place out of 110 teams.\
**Note**: The data is already splitted with 80% - 20% ratio to training and testing sets respectively, so a part of the data is already separated for final testing and will use the training set for train and validation.

## Methodology
### EDA
The dataset exploration and visualization resulted in very important insights about the data, including:\
1- Boxplot of weather features shows minimal outliers in some features.\
2- Data distribution using Histogram shows that 'Visibility', 'Solar radiation', 'Rainfall', and 'Snowfall' are skewed\
3- 
- Boxplot for 'Hour' shows that the rental demand witness significant increase from 7 to 9 AM and then from 17 to 20 PM, which are the working hours in seoul.
- Boxplot for 'Seasons' shows that hot seasons (summer & Spring) have more rental rates.
 
**Creating some additional features from the date/time variable like 'day of weak', 'month', 'year' and 'weak of year'**\
4- 
- Boxplot for 'month' supports what we get from seasons that summer months (June, July) have the most rental rate.
- 'Day of weak' and 'Holiday' boxplot assure that the rental rate increases at working days.
\
5- Pearson linear correlation shows a multicolinearity between Temperature and Dew point temperature.

### Preprocessing
All preprocessing steps and feature engineering are combined and implemented in the DataPrep() function with comments explanation for each step.\
1- Renaming features with unknown characters.\
2- Convert Date column type to datetime type.\
3- Feature engineering:
- Add Day, month, year and weak of year features.
- Add 'RentDemand' feature for high (1) and low (0) working hours.\
- Grouping day hours into 4 ranges 'night','morning','afternoon' & 'evening'.\
- Grouping continous features Rainfall into 3 groups:\
0 --> low\
0-5 --> middle\
above 5 --> high
- Grouping continous features Snowfall into 3 groups:\
0 --> low\
0-1.2 --> middle\
above 1.2 --> high
- As Hours, days, months and seasons are **cyclic features** i.e. hr23 and 0 are very close, we want the model to understand this cycle.***get their sin and cos components.***
- Adding interaction features.
- Adding column of total solar radiation per day.
- Adding column to identify days where there is no rain and snow combined and days of any.
- Adding K-means clustering column of temperature and dew point temp cols.
- Applying K-means clustering on the dataset but after scaling.
\
4- Categorical features encoding:
- Binary encode 'Functioning Day' and 'Holiday' features.
- Target-based encoding for 'dow', 'month', 'Seasons' and 'Hour' columns.
\
5- Feature Scaling:\
Based on the data distribution we noticed that some features follow normal distribution, some are left or right skewed, so we will apply different scaling techniques based on the feature nature and by several trials these scaling techniques gave best results:
- Min-Max scaling left skewed cols (Snowfall and Rainfall).
- Log transform wind speed, humidity and Solar radiation.
- Reciprocal transformation for Visibility feature.
- Calculate Average Rent per hour and day with log-transformation for **training data** only and then **MAP** the values to the test set according to each hour or day.

### Model Training
- The data is splitted to train and validation sets with 90%-10% ratio.
- Due to multicolinearity after adding several features, 'Snowfall','Hour' and 'Dew point temperature' are dropped and this improved the model.
- XGBoost and CatBoost models are both trained on the dataset and the average of the their predictions is considered as the final prediction.

### Results
The used metrics are RMSLE, RMSE and R2-score.
- Training set:\ 
RMSLE= 0.2096\
RMSE= 114.4508\
R2-score= 0.9695

- Validation set:\ 
RMSLE= 0.1428\
RMSE= 100.1943\
R2-score= 0.9754

- Test set:\
RMSLE= 0.3657\
RMSE= 173.0572\
R2-score= 0.9233