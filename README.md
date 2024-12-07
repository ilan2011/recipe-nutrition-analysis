# Recipe Nutrition Analysis

**By**: Ilan Breines  
**Contact**: ilanbrei@umich.edu

## Introduction

This analysis explores recipe data from Food.com, one of the largest recipe-sharing platforms on the internet. The dataset contains recipes and their nutritional information, sourced from user submissions since 2008. Our central question is: **Can we predict a recipe's calorie content based on its other nutritional information and characteristics?**

### Why This Matters
Understanding the relationship between different nutritional components and calorie content is crucial for:
- Home cooks trying to plan healthy meals
- People managing their dietary intake
- Recipe developers working on health-conscious alternatives
- Anyone interested in understanding how different nutrients contribute to overall caloric content

### Dataset Overview
The dataset contains 215,772 rows with detailed nutritional and preparation information as well as recipe ratings.

Relevant columns for our analysis:
- `calories`: Number of calories per serving (our prediction target)
- `total_fat`: Total fat content as percentage of daily value (PDV)
- `protein`: Protein content as PDV
- `carbohydrates`: Carbohydrate content as PDV
- `sugar`: Sugar content as PDV
- `sodium`: Sodium content as PDV
- `saturated_fat`: Saturated fat content as PDV
- `n_steps`: Number of steps in the recipe preparation
- `n_ingredients`: Number of ingredients used

## Data Cleaning and Exploratory Data Analysis

## Data Cleaning Process

Several critical cleaning steps were necessary to prepare yhe recipe data for analysis:

1. **Merging Recipe and Rating Data**
  - Performed a left join between recipes and ratings datasets to preserve all recipes even without ratings
  - This ensures all recipes are included in our analysis, regardless of review status

2. **Handling Cooking Times**
  - Filtered out recipes with preparation times beyond the 1st and 99th percentiles
  - This removes likely data entry errors (e.g., recipes claiming to take several days) and extremely unusual recipes
  - Keeps our analysis focused on typical home cooking scenarios

3. **Rating Processing**
  - Replaced ratings of 0 with NaN values since 0 ratings on Food.com indicate missing ratings rather than actual zero-star reviews
  - Calculated average rating per recipe using transform('mean') to maintain data alignment
  - Counted number of ratings per recipe to understand review frequency
  - Filled missing average ratings with 0 to indicate unrated recipes

4. **Deduplication**
  - Used drop_duplicates(subset='id') to keep only one instance of each unique recipe
  - This prevents popular recipes with many reviews from being overrepresented in our analysis
  - Ensures our calorie predictions are based on distinct recipes

5. **Nutritional Information Extraction**
  - Split the combined 'nutrition' string into separate columns for each component
  - Converted nutrition values from strings to numeric format for analysis
  - Original format was a list-like string: '[calories, total_fat, sugar, sodium, protein, saturated_fat, carbohydrates]'

6. **Tag Processing**
  - Cleaned recipe tags by removing brackets, quotes, and extra whitespace
  - Split tag strings into lists for potential categorical analysis

7. **Outlier Removal**
  - Removed recipes with nutritional values above the 99th percentile
  - This step ensures our analysis isn't skewed by potential data entry errors
  - Applied to: calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates

### Missing Values Analysis
I performed a programmatic check for missing values in all relevant columns and found no missing values. Therefore, no imputation was necessary for our analysis. This completeness is likely due to Food.com's submission requirements.

Here's what the cleaned dataset looks like (displaying only columns relevant to our analysis):

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                               | nutrition                                     |   n_steps | steps                                              | description                                        | ingredients                                        |   n_ingredients |          user_id |   recipe_id | date       |   rating | review                                             |   avg_rating |   n_ratings |   calories |   total_fat |   sugar |   sodium |   protein |   saturated_fat |   carbohydrates |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:---------------------------------------------------|:----------------------------------------------|----------:|:---------------------------------------------------|:---------------------------------------------------|:---------------------------------------------------|----------------:|-----------------:|------------:|:-----------|---------:|:---------------------------------------------------|-------------:|------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', ' | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 | ['heat the oven to 350f and arrange the rack in th | these are the most; chocolatey, moist, rich, dense | ['bittersweet chocolate', 'unsalted butter', 'eggs |               9 | 386585           |      333281 | 2008-11-19 |        4 | These were pretty good, but took forever to bake.  |            4 |           1 |      138.4 |          10 |      50 |        3 |         3 |              19 |               6 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine',  | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing b | this is the recipe that we use at my school cafete | ['white sugar', 'brown sugar', 'salt', 'margarine' |              11 | 424680           |      453467 | 2012-01-26 |        5 | Originally I was gonna cut the recipe in half (jus |            5 |           1 |      595.1 |          46 |     211 |       22 |        13 |              51 |              26 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', ' | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart b | since there are already 411 recipes for broccoli c | ['frozen broccoli cuts', 'cream of chicken soup',  |               9 |  29782           |      306168 | 2008-12-31 |        5 | This was one of the best broccoli casseroles that  |            5 |           4 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  | ['time-to-make', 'course', 'cuisine', 'preparation | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 | ['freheat the oven to 300 degrees', 'grease a 10-i | why a millionaire pound cake?  because it's super  | ['butter', 'sugar', 'eggs', 'all-purpose flour', ' |               7 | 813055           |      286009 | 2008-04-09 |        5 | don't let the calories and fat grams scare you off |            5 |           1 |      878.3 |          63 |     326 |       13 |        20 |             123 |              39 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  | ['time-to-make', 'course', 'main-ingredient', 'pre | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 | ['pan fry bacon , and set aside on a paper towel t | ready, set, cook! special edition contest entry: a | ['meatloaf mixture', 'unsmoked bacon', 'goat chees |              13 |      2.20436e+06 |      475785 | 2012-03-07 |        5 | Delicious!!!!! -- the goat cheese made the differe |            5 |           2 |      267   |          30 |      12 |       12 |        29 |              48 |               2 |

### Calorie Distribution and Key Relationships

The distribution of calories across recipes shows a right-skewed pattern, with most recipes containing moderate calorie counts. This aligns with what we might expect from real-world recipes, where extreme calorie counts are less common.

<iframe
  src="assets/cal_distribution.html"
  width="10001"
  height="600"
  frameborder="0"
></iframe>

Our analysis revealed strong correlations between calories and other nutritional components:

<iframe
  src="assets/fat_calories.html"
  width="1000"
  height="600"
  frameborder="0"
></iframe>

The scatter plot demonstrates a strong positive correlation between total fat content and calories, suggesting that fat content is a key predictor of a recipe's calorie count.

Here's how nutritional values and recipe characteristics vary across calorie quintiles:

| calories   |   total_fat |   protein |   carbohydrates |   sugar |   sodium |   saturated_fat |   n_ingredients |   n_steps |
|:-----------|------------:|----------:|----------------:|--------:|---------:|----------------:|----------------:|----------:|
| 0-20%      |        4.89 |      4.75 |            3.1  |   20.98 |     7.49 |            5.74 |            7.42 |      7.96 |
| 20-40%     |       11.9  |     14.32 |            6.76 |   35.88 |    12.66 |           14.05 |            8.66 |      9.18 |
| 40-60%     |       19.79 |     25.46 |            9.54 |   44.43 |    18.7  |           24.07 |            9.32 |      9.87 |
| 60-80%     |       30.41 |     37.88 |           12.8  |   54.82 |    25.5  |           38.49 |            9.91 |     10.79 |
| 80-100%    |       57.31 |     58.81 |           18.79 |   68.64 |    38.04 |           69.57 |           10.71 |     12.08 |

This table reveals several interesting patterns:
- As calories increase, all nutritional components tend to increase proportionally
- Higher-calorie recipes tend to be more complex, with more ingredients and steps
- The jump in nutritional values is particularly dramatic in the highest calorie quintile (80-100%)
- Sugar content shows the smallest relative increase across quintiles compared to other nutrients

## Framing a Prediction Problem

I formulated my analysis as a regression problem to predict recipe calorie content from other nutritional and recipe characteristics.

### Problem Statement
- **Task Type**: Regression (predicting a continuous numerical value)
- **Response Variable**: Calories per serving
- **Features Available at Prediction Time**: 
 - Other nutritional information (fats, proteins, carbohydrates, etc.)
 - Recipe characteristics (number of steps, number of ingredients)
 - Note: We can't use ratings or reviews as features since these would only be available after a recipe is published

### Why Predict Calories?
I chose calories as the prediction target because:
1. It's a critical nutritional metric that directly impacts meal planning and dietary decisions
2. Accurate calorie information is essential for recipe websites and health-focused applications
3. Unlike subjective measures like ratings, calories can be objectively measured and validated

### Evaluation Metric Choice
I selected Mean Absolute Error (MAE) as the evaluation metric over alternatives like MSE/RMSE for several reasons:

1. **Interpretability**: MAE represents the average number of calories our predictions are off by, making it directly meaningful to users. For example, "our predictions are off by an average of 77 calories" is more interpretable than a squared error value.

2. **Robustness**: Recipe calories naturally have some outliers (very light snacks vs. heavy meals). MAE is less sensitive to these outliers compared to squared error metrics, providing a more reliable measure of typical model performance.

3. **Linear Scale**: The impact of calorie prediction errors is relatively linear so being off by 100 calories isn't necessarily 4 times worse than being off by 50 calories. This matches MAE's linear error scale better than MSE's quadratic scale.

Alternative metrics I considered but didn't choose:
- Mean Squared Error (MSE): Would penalize large errors too heavily relative to their practical impact
- Root Mean Squared Error (RMSE): While in the same units as calories, still overemphasizes large errors
- Mean Percentage Error (MPE): Could be misleading for low-calorie recipes where small absolute errors would appear as large percentages

## Baseline Model

My baseline model aims to predict recipe calories using just two fundamental nutritional features:

### Features
I selected two quantitative features for my initial model:
1. Total Fat (PDV)
2. Protein (PDV)

Both features are continuous numerical values representing the Percentage of Daily Value (PDV) of these nutrients. No encoding was necessary as these features were already in numerical format after our initial data cleaning.

Feature Breakdown:
- Quantitative features: 2 (total fat, protein)
- Ordinal features: 0
- Nominal features: 0

### Model Performance
Using a simple linear regression, our baseline model achieved:
- Mean Absolute Error (MAE): 77.35 calories
- For context:
 - Average recipe in dataset: 332.59 calories
 - Error represents about 23.3% of mean calorie content

### Model Assessment
Is this a "good" model?

Strengths:
1. Relatively simple and interpretable
2. Uses just two readily available nutritional metrics
3. Average error of 77 calories is reasonable for meal planning purposes
4. Captures the strongest correlations I observed in the exploratory analysis (total_fat correlation: 0.85, protein correlation: 0.67)

Limitations:
1. Ignores other potentially useful nutritional information (carbohydrates, sugar, etc.)
2. Doesn't account for recipe complexity (number of steps, ingredients)
3. Doesn't consider interactions between nutritional components

While the baseline model provides a reasonable starting point, there's clear room for improvement by incorporating additional features and more sophisticated modeling techniques in our final model.

## Final Model

### Feature Engineering Rationale
Building upon our baseline model's total fat and protein features, I added several additional features chosen based on nutritional science and cooking principles:

1. **Basic Nutritional Components**:
  - Carbohydrates: A primary source of calories alongside fats and proteins
  - Sugar: Indicates caloric density from simple carbs
  - Sodium: Can indicate preservation methods and cooking styles
  - Saturated Fat: Distinguishes between different types of fats with varying caloric impacts

2. **Recipe Complexity Metrics**:
  - Number of Steps: More preparation steps often indicate more complex cooking methods that can affect caloric content
  - Number of Ingredients: More ingredients typically suggest more complex dishes with potentially higher calorie content

3. **Engineered Nutritional Ratios**:
  - Protein-to-Fat Ratio: Helps identify recipe types (e.g., high-protein/low-fat health foods vs. rich desserts)
  - Carbs-to-Protein Ratio: Distinguishes between carb-heavy dishes (like pasta) and protein-focused meals
  - Saturated Fat Ratio: Indicates proportion of unhealthy fats, helping identify cooking methods and ingredient choices

### Modeling Choices
I selected Lasso regression as our final model for several reasons:
- Performs automatic feature selection through L1 regularization
- Maintains interpretability of coefficients
- Can handle correlated features (like our various nutritional ratios)

### Hyperparameter Selection

For our Lasso regression model, we'll tune the alpha parameter, which controls the strength of regularization. This is important because:

1. With many nutritional features that may be correlated, we need to find the right balance between fitting the data and preventing overfitting
2. The alpha parameter will help identify which features are most important for prediction by potentially shrinking less important coefficients to zero

### Hyperparameter Tuning
- Used GridSearchCV with 5-fold cross-validation
- Tuned Lasso's alpha parameter across 50 values from 10^-4 to 1
- Best alpha = 1.0000, indicating strong regularization was beneficial
- This suggests some features were less important for prediction


### Model Performance
The final model achieved an MAE of 10.75 calories, compared to the baseline's 77.35 calories: an 86% improvement. 

Key insights from feature coefficients:
1. Carbohydrates (11.54), total fat (5.59), and protein (2.21) emerged as the strongest predictors
2. Sugar and number of ingredients had minimal impact (coefficients near zero)
3. Some engineered features showed negative coefficients, suggesting they help correct overestimates from the primary nutritional features

This substantial improvement validates our feature engineering approach based on nutritional science, though the strong regularization suggests some engineered features were less useful than theoretically expected.