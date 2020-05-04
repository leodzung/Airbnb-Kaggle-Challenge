# Airbnb Kaggle Challenge
This is my personal attempt to revisit the [Airbnb Chanllenge on Kaggle](https://www.kaggle.com/c/airbnb-recruiting-new-user-bookings).

## Overview
The competition was hosted on Kaggle 4 years ago by Airbnb as a recruiting competition. Kagglers who impressed Airbnb received an interview invite to join Airbnb's Data Science and Analytics team. With such attractive prospective to work for a fast growing start-up, this 3-months competition attracted nearly 1,500 participants.

The competition uses NDCG (Normalized discounted cumulative gain) @k where k=5([Read more](https://www.kaggle.com/c/airbnb-recruiting-new-user-bookings/overview/evaluation)).

The top team scored 0.88208 in public leaderboard and 0.88697 in private leaderboard. Unfortunately, there was no write-up on how this team achieved such high score on the leaderboard. The 2nd place team with the public leaderboard score of 0.88209 and private leaderboard score of 0.88682, which is marginally lower than the 1st place. They shared [their solution in R]( https://github.com/Keiku/kaggle-airbnb-recruiting-new-user-bookings). This solution is using ensembles of 18 models, mostly XGBoost, on different variation of 1312 base features.

### Why LightGBM
At that time, LightGBM wasn't born and neural networks was still unpopular due to the expensive computational cost. This intrigues my curiosity - how would these novel approaches perform on this competition, compared with XGBoost, which is very popular at the time the competition was held?

// TODO
Why I decided to use LightGBM
What makes LightGBM different than xgboost

## Feature Engineer

### Extract Frequency Counts
For each user, I count the number of time 

The data provided by Airbnb has an important feature: the cut-off time between the training data and testing data -07/01/2014. Interestingly, the session informations are only available for the data points from 2014 onward. With this in mind, I ran the model on all data and data after 2014 (fresh data) and ensemble the prediction with weights (0.4 and 0.6 respectively), as I deemed fresh data is more important.

// TODO
What features did I end up using?
What features did I create?
What additional features not currently in the data would best help I answer my question?

### Model Explanation
// TODO
