# Airbnb Kaggle Challenge
This is my personal attempt to revisit the [Airbnb Chanllenge on Kaggle](https://www.kaggle.com/c/airbnb-recruiting-new-user-bookings).

## Overview
The competition was hosted on Kaggle 4 years ago by Airbnb as a recruiting competition. Kagglers who impressed Airbnb received an interview invite to join Airbnb's Data Science and Analytics team. With such attractive prospective to work for a fast growing start-up, this 3-months competition attracted nearly 1,500 participants.

## Evaluation Metric
The competition uses NDCG (Normalized discounted cumulative gain) @k where k=5([Read more](https://www.kaggle.com/c/airbnb-recruiting-new-user-bookings/overview/evaluation)). This is a measure of ranking quality that takes into account:
1. Relevance of every recommendation
2. Order of each recomendation. The item with highest relevance should be recommended first.
3. Variety in the number of recommendation. Depending on various factors, the number of recommendations may vary. For example, for new users, there might be more recommendation to catch a wider net of posibility, while for seasoned users, more historical data allows more targeted and thus narrow recommendation.

The top team scored 0.88208 in public leaderboard and 0.88697 in private leaderboard. Unfortunately, there was no write-up on how this team achieved such high score on the leaderboard. The 2nd place team with the public leaderboard score of 0.88209 and private leaderboard score of 0.88682, which is marginally lower than the 1st place. They shared [their solution in R]( https://github.com/Keiku/kaggle-airbnb-recruiting-new-user-bookings). This solution is using ensembles of 18 models, mostly XGBoost, on different variation of 1312 base features.

## LightGBM
### Why LightGBM
At the time of the competition (early 2016), XGBoost was the algorithm of choice for many data scientists, and it indeed helped winning many Kaggle competitions. A year later (Jan 2017), LightGBM first stable version was released by Microsoft. What kind of results that the participants in the competition would have if they used LightGBM? This is an attempt to answer this question.

### What is LightGBM
LightGBM is a efficient, distributed gradient boosting algorithm. It has several advantages over XGBoost that will be shown in the benchmark, including:
1. Faster training speed
2. Better accuracy
3. Support GPU learning

References:
[readthedocs.io] (https://lightgbm.readthedocs.io/en/latest/)

## Data Exploration
There are a total of 275547 users, 213451 in the training set and 62096 in the test set.

### Missing data
Taking a look at the dataframe, we can easily that there are missing data in 
1. date_first_booking in form of NaN. This feature has around 58% of NaN values because it is not present for the test users in the test dataset, therefore, this feature is not used for modeling.
2. gender in the form of '-unknown-' string. There are nearly 47% of missing values.
3. age in the form of NaN. 42% of this feature is NaN. More over, when we do have the age data, it is not reliable. There are 830 users that have age more than 122 (longest human lifespan to date), and 188 users with age lower than 18 (legal age for Airbnb users). I have decided to replace that are outside of the range [13, 95] to NaN.

### Visualization
![Gender](/visualization/gender.png)

This confirm the amount of missing data in gender. There are a slightly more female than male, which either could mean that Airbnn attract more female users than their male counterparts, or could also mean that female users more likely to identify their gender on Airbnb more than their male counterparts do.

![Destination by Gender](/visualization/destination%20by%20gender.png)

This so no big differences between the two genders when it comes to travelling. But this is useful to know the favorite destination: US. Let's take a closer look.

![Destination by Gender](/visualization/destination.png)

What I notice that there are a significant number of Airbnb users (96%) has english as their language of choice. This can explain the inherant bias toward US as the users make their first booking.

![Destination by Gender](/visualization/age.png)

The largest age group on Airbnb is between 25 and 40. Notice that the curve is not smooth because there are a lot of missing data for this feature.

## Feature Engineer

### Session Data

Session data tells a lot about user behavior:
1. What action they take, what is the action type. 
2. What device they are using.
3. How long is the session in seconds.

This type of information is very helpful to predict whether or not the users will make their first booking. The more action they take, the more time they actively spend on these sessions, the more likely that they are serious with their travelling plan and the more ready they are to finalized their booking.

Therefore, for each of the features in the session data, I extracted frequency counts for categorical columns and mean, standard deviation, median, and also the skewness of the numerical ones.

The data provided by Airbnb has an important feature: the cut-off time between the training data and testing data -07/01/2014. Interestingly, the session informations are only available for the data points from 2014 onward. With this in mind, I ran the model on all data and data after 2014 (fresh data) and ensemble the prediction with weights (0.4 and 0.6 respectively), as I deemed fresh data is more important.

### Dates

This is a classic time series prediction problem. Users are more active and make the first booking on a certain time of the , on a certain day of the week, and certain month of the year. This is evident in the data exploration section of the notebook - for example, Airbnb users seem to be most active on Tuesday. 

For each date in the dataset, I parsed out the year, month, day and day of the week and use them as the features in my  final model.

### Most important features

I ended up create 157 features, which is very light compared with the top teams. With thoughtful selection of the most important features and tuning hyper-parameters, LightGBM allows me to have a comparable results with the top teams with much less feature engineering.

![Destination by Gender](/visualization/top_10_features.png)

What features did I end up using?
What features did I create?
What additional features not currently in the data would best help I answer my question?

### Model Explanation
// TODO

## Parameters Tuning
