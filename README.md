# recipes-ratings-model

## Framing the Problem
Data Cleaning
Some of the initial data cleaning efforts took place before merging the two datasets together. We converted all the ratings in the interaction dataframe that were 0 to np.nan, which allowed us to ignore them in the calculations for the mean.

Once we had a merged dataset, we had to make sure that all the columns were the correct types. For example, we had to convert the submitted column, which was originally a string, to datetime objects, which allowed us to make visualizations and analyses using that data. Many of the columns also started as strings which only looked like lists, rather than actually being lists of strings. To solve this, we split these strings and added the components to lists. This allowed us to actually access individual components of these lists. Interestingly, the ingredients column also had a similar problem, but because we only used it to check if chocolate was an ingredient, we did not convert it to a list. We instead searched the whole string for occurrences of the string “chocolate”, which allowed us to create the chocolate column in the data frame.
## Baseline Model
The baseline model that we created predicts 'protein (PDV)'. The features that we used were all quantitive features which were 'minutes','calories','sodium (PDV)', and 'carbohydrates (PDV)'. To create the features, we used a preprocessor with ColumnTransformer that would transform our specified columns. We transformed the 'minutes' column by created a square root function and using FunctionTransformer() to apply the transformation to the column, which took the square root of each value. We also used the StandardScaler() transformer class to standardize the columns 'sodium (PDV)','carbohydrates (PDV)', and 'calories'. For the baseline model, we used Linear Regression. The current performance of our model is not ideal. The score or R^2 of our model on the training data is 0.4049. The score for our model on the testing data is 0.3977. Although the variance is low for our predictions, the accuracy is pretty low meaning that the model is not ideal. Additionally, the root mean squared error (RMSE) for the train model is 29.72 and 30.037 for the test model. Although the RMSE for test is larger, I do not think that we can arrive to the conclusion that this current model can necessarily not generalize well or generalize well since our accuracy is still pretty low. 

## Final Model

## Fairness Analysis
The two groups we chose for the fairness analysis are "old recipes" and "new recipes" with the cut off year being 2015. So recipes before 2015 are classified as "old" and recipes after 2015 are classified as "new". For our evaluation metric, we chose RMSE because we were doing a Regressor model (KnneighborsRegressor). 
Null Hypothesis: Our model is fair. Its RMSE for old and new recipes are roughly the same, and any differences are due to random chance.
Alternative Hypothesis: Our model is unfair. Its RMSE for new and old recipes are not roughly equal.

For the test statistic, we found the differences in RMSE for the new and old groups. We used a significance level of 0.05. Our resulting p-value was 0.67 which is greater than the significance level of 0.05 meaning that fail to reject the null so we cannot conclude that our model is fair. It is likely that our model is fair.
