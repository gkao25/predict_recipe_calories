# Predict Recipe Calories

Project for DSC 80 at UCSD

By Gloria Kao

Our exploratory data analysis on this dataset can be found [here](https://gkao25.github.io/cooking_time_avg_rating/).


# Framing the Problem

## Problem Identification

*Data cleaning and EDA is done previously in Project 3.*

**Prediction Problem**: Predict calories of recipes. 

**Response Variable**: calories (the column `calores (#)` in the dataset)
We chose this as the variable because it represents the number of calories in a recipe. At the time of prediction, we have all the nutritional values of a recipe in our dataset already. 

**Model**: We will use linear regression model because the response variable is numerical.

**Evaluation Metrics**: We will use RMSE to measure how fit the linear regression model is, and $R^2$ as our evaluation metrics to measure the strength of the relationship. 

A look at the cleaned dataset that contains only the calories and nutritional valus of a recipe. All columns are numerical (quantitative).

|    |   calories (#) |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |
|---:|---------------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|
|  0 |          138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 |
|  1 |          595.1 |                46 |           211 |             22 |              13 |                    51 |                    26 |
|  2 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 |
|  3 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 |
|  4 |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 |

A look at the scatter matrix to discover features that may have associations with calories.

<iframe src="assets/nutritions_scatter_matrix.html" width=800 height=600 frameBorder=0></iframe>

There seem to be positive correlations between calories/total fat, calories/sugar, calories/protein, calories/saturated fat, calories/carbohydrates, carbohydrates/sugar, saturated fat/total fat, protein/total fat. We can pick some of these variables as features to train our model. 


# Baseline Model

We're using a normal linear regressio model to predict the calories. The baseline model will have two features, `total fat (PDV)` and `sugar (PDV)`. Both of them are quantitative features and not transformed. 

To generalize the model to unseen data, we have done a train-test split. The model is `fit` on a training set only, and evaluates to an RMSE of 195.505 and a $R^2$ of 0.887. The test set evaluates to an RMSE of 198.644 and a $R^2$ or 0.884. RMSE is the error of our prediction, and $R^2$ is the coefficient of determination which describes the quality of a linear fit. The baseline model is pretty good because $R^2$ is high, but the RMSE is also high, which leads us to believe that the model can be improved to lower the error. 


# Final Model

We added two new features, `protein (PDV)` and `carbohydrates (PDV)`, so now we have a total of four quantitative features. From the previous scatter matrix, both protein and carbohydrates display positive correlations to calories, so we believe they would improve our model's performance. Furthermore, from common knowledge we know that recipes like steak and bread are high in calories, which are protein and carbohydrate, respectively. 

The modeling algorithm involves setting the `y` variable as calories, and all other variable as `X`. Because our model is a standard linear regression (i.e. OLS), we did not tune for any hyperparameters. We standardized the four quantitative features `total fat (PDV)`, `sugar (PDV)`, `protein (PDV)`, and `carbohydrates (PDV)` in a Pipeline, and left the rest untouched. 

Similar to the baseline model, we have done a train-test split to generalize the final model to unseen data. The training set evaluates to an RMSE of 38.810 and a $R^2$ of 0.995. The test set evaluates to an RMSE of 36.257 and a $R^2$ or 0.995. Compared to the baseline model's performance, both RMSE and $R^2$ improved significantly. 


# Fairness Analysis

According to the FDA, "5% DV or less of a nutrient per serving is considered low, 20% DV or more of a nutrient per serving is considered high," and anything in between would be normal ([source](https://www.fda.gov/food/new-nutrition-facts-label/lows-and-highs-percent-daily-value-new-nutrition-facts-label)). However, because we only need to compare the fairness between two groups, we will have...

**Group X**: "low carbohydrates", carbohydrates PDV $<$ 20

**Group Y**: "high carbohydrates", carbohydrates PDV $\ge$ 20

**Evaluation Metric**: RMSE

**Null Hypothesis**: Our model is fair. Its RMSE for low carbohydrates and high carbohydrates are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis**: Our model is unfair. Its RMSE for high carbohydrates is lower than its RMSE for low carbohydrates.

**Test Statistic**: difference in RMSE

**Significance Level**: 0.05

<iframe src="assets/diff_in_rmse.html" width=800 height=600 frameBorder=0></iframe>

After performing a permutation test shuffling the groups, we get a p-value of 0.136, which is greater than our significance level of 0.05. Therfore, we **fail to reject** the null hypothesis. There is no significant difference in RMSE between the low carbohydrates and high carbohydrates groups, and we can conclude that **our model is fair**. 