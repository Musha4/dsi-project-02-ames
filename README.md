
# Project 2 - Ames Housing Data and Kaggle Challenge

## Problem Statement

Challenge: You are tasked with creating a regression model based on the Ames Housing Dataset. This model will predict the price of a house at sale.

The class was provided with a training dataset (train.csv) and test dataset (test.csv). The training dataset includes the target variable (sale price). We were assigned the challenge of generating a regression model using the training data, predicting values for the target using the test data, and submitting our predictions to the private Kaggle competition for the class to be scored against the actual target values. The preliminary scores given during the competition are based on only 30% of the test data. At the conclusion of the competition, the final submissions are scored against the remaining 70% of the test data. (This is to discourage overfitting the model to the data used for preliminary scores.)

## Presentation Prompt

Presentation Prompt:  
Zillow will often indicate "Estimated home price" to inform users about the market rates of homes, even when they are not yet explicitly for sale. More accurate depictions of home values translates to more confidence in Zillow's product, and will therefore grow their user base (translating to more advertising revenue).

You've been recently hired at Zillow and your first work assignment is to build a model that will make the best possible prediction of home values in Ames, Iowa (They reserve the more exciting cities for seasoned veterans, this is your opportunity to prove yoursel!). You will be making a presentation to Zillow's semi-technical staff about the process that led you to your final model and predictions.

Some recommendations of what to include: compelling visuals, significant insights uncovered, noteable obstacles, description of the dataset, the most informative features, interpret coefficients, any features you engineered, any outside data used, description of scaling or regularization if used, any hyperparameter tuning, final model performance.



## My Process

### Data cleaning and EDA
Checking data type: Per data documentation here: http://jse.amstat.org/v19n3/decock/DataDocumentation.txt the following columns have numeric values but data is categorical:
- MS SubClass (Nominal): Identifies the type of dwelling involved in the sale.
- Mo Sold (Discrete): Month Sold (MM) (unique values 1-12)
- Yr Sold (Discrete): Year Sold (YYYY) (unique values: 2006, 2007, 2008, 2009, 2010)  

I converted these columns into strings so they could be used to create dummy columns with the other categorical columns later.  

'Id' and 'PID' are property ID numbers that should not affect sale price. I removed these columns.  

Missing values: I checked for null values by column. Columns with a high percentage of null values (higher than 45% null) were removed. Columns removed: 'Pool QC', 'Misc Feature', 'Alley', 'Fence', 'Fireplace Qu'. Remaining null values were assigned to 0 for numeric data and 'null' for strings. This is not the best method of imputing missing values. I tried filling null values in numeric columns with the mean of the column, but the Kaggle score got slightly worse (increased from 29372.39816 to 30391.41153). A future improvement to the project I would like to try would be filling values with the median/most common value for that column.

For my first "quick and dirty" model, I used numeric columns only, dropped all columns with null values in the train and test data, and created a linear regression model. The score for this submission was 33454.57283, not as bad as I had expected. There were some unexpected factors improving the performance of this model, such as the fact that 'PID' was actually correlated to the sale price in the training data. However, I did not include 'Id' or 'PID' in my final model as it is unclear why the correlation exists and I feel it is unlikely to be the same for new data or data from a different city.

### Preprocessing and Modeling
Interaction Terms: I created an interaction term for total SF (sum of individual values for basement, 1st and 2nd floor SF). This variable does show a higher correlation with sale price than the other individual floor SF values, which I dropped from the features to avoid collinearity.  

Categorical variables: Categorical variables were converted to one-hot encoded columns using .get_dummies()  

The training data was split 95%/5% using scikit-learn train_test_split for cross-validation. Data was scaled using scikit-learn StandardScaler. A future improvement to the project would be to try taking the log of the target variable before fitting the model, as was pointed out in other presentations that the sale price distribution is skewed. (The final predictions would then have to be exponentiated to get the correct values.)

I tested and evaluated models using linear regression, lasso, and ridge algorithms. After adding dummy columns, linear regression began predicting negative values for sale price. Lasso and ridge did not have this problem. Possible improvement: try scaling variables for linear regression.

Data & Model | R^2 score | Kaggle score (RMSE)
--- | --- | ---
Linear regression, use numeric data only, drop rows & columns with null values | 0.83358 | 33,455
Linear regression, use numeric data only, replace nulls with 0 | 0.83686 | 33,847
RidgeCV, replace nulls with 0  | 0.93567 | 47,954
LassoCV, replace nulls with mean | 0.91776 | 30,391
LassoCV, replace nulls with 0 | 0.90975 | 29,372

## Conclusion

Replacing null values with 0 and using LassoCV algorithm produced the best predictions based on the RMSE score from Kaggle.  

Next steps: 
- improve model: Having the most accurate sale price predictions vs competitors is Zillow's goal
- better identification of important features that affect sale price and quantifying their effect: Being able to make recommendations on the likely increase in home value from various improvements that homeowners/sellers could make would be useful information for Zillow to provide for customers
