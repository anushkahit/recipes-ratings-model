# recipes-ratings-model
Here is the link to our Exploratory Data Analysis on this dataset: https://anushkahit.github.io/recipes-ratings-analysis/

## Framing the Problem
### Data Cleaning
For this project, the area we wanted to focus on was nutritional statistics. After spending time with this dataset in an earlier project, we knew that there was a lot of nutritional data within the dataset, and it would be an interesting topic to explore. After some discussion, the topic we decided on was Protein. More specifically, in this project, we built a model (or two) that predicted the Protein Content of a recipe.

This is a regression problem, as we are trying to predict the numeric values of ‘protein (PDV)’ using the other information available to us in the dataset. We will be using RMSE as a way to judge the performance of our model, as it will allow us to more easily quantify just how far our predictions are from actual values. In this dataset, all information will be available to us, given that we are trying to predict the protein content of new recipes, which is not something that would happen before any of the other items in the dataset.

In terms of the data cleaning process, we were able to reuse much of the code from our previous project, which looked at the effect of chocolate on recipes’ ratings. However, we had to do further data processing on the nutritional information section in particular, as it was something we did not use before. To process this data, we decomposed the list of nutritional information into additional columns in our recipes_rated dataframe to allow for easier access and computation in the future. 

For ease of work, we decided to drop rows with NaN values in the ratings and name columns, as they were columns we were planning on using for features. We also dropped rows that had outliers in the protein column, as those would skew our resulting model, while likely being mistypes or false information. Overall, we ended up dropped about 1000 rows of data, which left us with over 80,000 rows to analyze, which was more than enough to build, train, and test a robust regression model.

<iframe src="assets/pred.html" width=800 height=600 frameBorder=0></iframe>

## Baseline Model
The baseline model that we created predicts 'protein (PDV)'. The features that we used were all quantitive features which were 'minutes','calories','sodium (PDV)', and 'carbohydrates (PDV)'. To create the features, we used a preprocessor with ColumnTransformer that would transform our specified columns. We transformed the 'minutes' column by created a square root function and using FunctionTransformer() to apply the transformation to the column, which took the square root of each value. We also used the StandardScaler() transformer class to standardize the columns 'sodium (PDV)','carbohydrates (PDV)', and 'calories'. For the baseline model, we used Linear Regression. The current performance of our model is not ideal. The score or R^2 of our model on the training data is 0.4049. The score for our model on the testing data is 0.3977. Although the variance is low for our predictions, the accuracy is pretty low meaning that the model is not ideal. Additionally, the root mean squared error (RMSE) for the train model is 29.72 and 30.037 for the test model. Although the RMSE for test is larger, I do not think that we can arrive to the conclusion that this current model can necessarily not generalize well or generalize well since our accuracy is still pretty low. 

## Final Model
After creating the baseline model, we decided to shift away from a Linear Regression model, as we thought that it was too simple, and was underfitting the data. For a future model, we were split between a KNeighborsRegressor and a DecisionTreeRegressor. After some research, we decided to move forward with a DecisionTreeRegressor. 

In terms of features, our baseline model was quite minimalist, which could have contributed to its underfitting problem. To combat this, one of the primary changes we made was considering even the non-transformed features (using remainder=“passthrough” instead of “drop”). We also decided to engineer a couple of our own features using the tags and name columns. By using a list of high protein foods, we essentially binarized whether a dish’s name contained a high-protein food, which we believed would help us predict whether a dish itself was high protein. Similarly, we extracted a few relevant tags (“high-protein” for example) from the list of all tags on recipes and assigned a new column which contained the number of relevant tags in each tag list. We hoped that both of these new features would better allow us to identify the contents of a dish, and assess its protein quantities. Like in the baseline model - we transformed “minutes” using a square root, hoping this would mitigate the effect of the extremely large values in the column. Another transformation we did was binarizing the number of steps and ingredients, as we noticed there were a lot of small dishes with a few ingredients/steps. Rather than using a quantitative feature, we decided to simplify it to small dishes and large dishes. We also binarized the rating column, as we noticed that a majority of ratings were above 4.5, with a very small deviation, which could reduce its effectiveness as a feature. We hoped that binarizing this feature would separate the good recipes from bad. Finally, we standardized sodium, carbs, and calories - as we felt these were the three most relevant nutritional statistics (most high protein meals are main dishes - salty and with accompanying carbs - ex. Meat pasta), so it would be helpful to have their values standardized before training the model. As mentioned before, we passed through the remaining columns in the dataframe. 

The model we chose was a DecisionTreeRegressor, which had a few hyperparameters to select, most importantly max_depth. To find out the best hyperparameters for the model and transformers, we ran a gridsearch, searching for the best combination of Binarizer threshold for n_steps and n_ingredients, as well max depth.  We did have to adjust the range of our search a few times to make sure we were getting the optimal values, but eventually we settled on a threshold of 12 (the notebook shows 6 but when we ran it previously it returned 12), and a max depth of 14. After finding the best hyperparameters, we recreated our pipeline and fit our training data to it. Our final model had a RMSE of about 8 on the training data, and 18 on the testing data, both of which were improvements on the baseline model. While 18 is still a fairly high value, we improved over 30% from the baseline. In addition, our r^2 value was about .76, which suggests a strong relationship between our predictions and the actual values. This can also be seen by the plot below, which further highlights where our final model went wrong. We struggled at the edges of the dataset - high protein especially, but in the main areas, it is a fairly strong predictor.
## Fairness Analysis
The two groups we chose for the fairness analysis are "old recipes" and "new recipes" with the cut off year being 2015. So recipes before 2015 are classified as "old" and recipes after 2015 are classified as "new". For our evaluation metric, we chose RMSE because we were doing a Regressor model (KnneighborsRegressor). 
Null Hypothesis: Our model is fair. Its RMSE for old and new recipes are roughly the same, and any differences are due to random chance.
Alternative Hypothesis: Our model is unfair. Its RMSE for new and old recipes are not roughly equal.

For the test statistic, we found the differences in RMSE for the new and old groups. We used a significance level of 0.05. Our resulting p-value was 0.67 which is greater than the significance level of 0.05 meaning that fail to reject the null so we cannot conclude that our model is fair. It is likely that our model is fair.
