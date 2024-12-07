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
The dataset contains 215,772 rows and 26 columns with detailed nutritional and preparation information as well as recipe ratings.

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

Several critical cleaning steps were necessary to prepare our recipe data for analysis:

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

Here's what our cleaned dataset looks like (displaying only first 50 chars of each cell):

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                                                                                               | nutrition                                     |   n_steps | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | description                                                                                                                                                                                                                                                                                                                                                                       | ingredients                                                                                                                                                                                                                             |   n_ingredients |          user_id |   recipe_id | date       |   rating | review                                                                                                                                                                                                                                                                                                                                           |   avg_rating |   n_ratings |   calories |   total_fat |   sugar |   sodium |   protein |   saturated_fat |   carbohydrates |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------|----------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|-----------------:|------------:|:-----------|---------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------:|------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings']                                                                        | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil', 'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted', 'remove from heat and let cool to room temperature', 'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated', 'add cooled chocolate and mix until uniform in color', 'add flour and stir until just incorporated', 'transfer batter to the prepared baking dish', 'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes', 'remove from the oven and cool completely before cutting']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven!                                                                                                              | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour']                                                          |               9 | 386585           |      333281 | 2008-11-19 |        4 | These were pretty good, but took forever to bake.  I would send it ended up being almost an hour!  Even then, the brownies stuck to the foil, and were on the overly moist side and not easy to cut.  They did taste quite rich, though!  Made for My 3 Chefs.                                                                                   |            4 |           1 |      138.4 |          10 |      50 |        3 |         3 |              19 |               6 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                                                                                                      | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder', 'set aside', 'in another mixing bowl , blend together the sugars , margarine , and salt until light and fluffy', 'add the eggs , water , and vanilla to the margarine / sugar mixture and mix together until well combined', 'add in the flour mixture to the wet ingredients and blend until combined', 'scrape down the sides of the bowl and add the chocolate chips', 'mix until combined', 'scrape down the sides to the bowl again', 'using an ice cream scoop , scoop evenly rounded balls of dough and place of cookie sheet about 1 - 2 inches apart to allow for spreading during baking', 'bake for 10 - 15 minutes or until golden brown on the outside and soft & chewy in the center', 'serve hot and enjoy !']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just use butter (softened) instead.                                                                                                                                            | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                                                                             |              11 | 424680           |      453467 | 2012-01-26 |        5 | Originally I was gonna cut the recipe in half (just the 2 of us here), but then we had a park-wide yard sale, & I made the whole batch & used them as enticements for potential buyers ~ what the hey, a free cookie as delicious as these are, definitely works its magic! Will be making these again, for sure! Thanks for posting the recipe! |            5 |           1 |      595.1 |          46 |     211 |       22 |        13 |              51 |              26 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                                                                                               | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']                                                                   |               9 |  29782           |      306168 | 2008-12-31 |        5 | This was one of the best broccoli casseroles that I have ever made.  I made my own chicken soup for this recipe. I was a bit worried about the tsp of soy sauce but it gave the casserole the best flavor. YUM!                                                                                                                                  |            5 |           4 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |
|                                      |        |           |                  |             |                                                                                                                                                                                                                                                                                                    |                                               |           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                                                                         |                 |                  |             |            |          | The photos you took (shapeweaver) inspired me to make this recipe and it actually does look just like them when it comes out of the oven.                                                                                                                                                                                                        |              |             |            |             |         |          |           |                 |                 |
|                                      |        |           |                  |             |                                                                                                                                                                                                                                                                                                    |                                               |           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                                                                         |                 |                  |             |            |          | Thanks so much for sharing your recipe shapeweaver. It was wonderful!  Going into my family's favorite Zaar cookbook :)                                                                                                                                                                                                                          |              |             |            |             |         |          |           |                 |                 |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  | ['time-to-make', 'course', 'cuisine', 'preparation', 'occasion', 'north-american', 'desserts', 'american', 'southern-united-states', 'dinner-party', 'holiday-event', 'cakes', 'dietary', 'christmas', 'thanksgiving', 'low-sodium', 'low-in-something', 'taste-mood', 'sweet', '4-hours-or-less'] | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 | ['freheat the oven to 300 degrees', 'grease a 10-inch tube pan with butter , dust the bottom and sides with flour , and set aside', 'in a large mixing bowl , cream the butter and sugar with an electric mixer and add the eggs one at a time , beating after each addition', 'alternately add the flour and milk , stirring till the batter is smooth', 'add the two extracts and stir till well blended', 'scrape the batter into the prepared pan and bake till a cake tester or knife blade inserted in the center comes out clean , about 1 1 / 2 hours', 'cool the cake in the pan on a rack for 5 minutes , then turn it out on the rack to cool completely']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | why a millionaire pound cake?  because it's super rich!  this scrumptious cake is the pride of an elderly belle from jackson, mississippi.  the recipe comes from "the glory of southern cooking" by james villas.                                                                                                                                                                | ['butter', 'sugar', 'eggs', 'all-purpose flour', 'whole milk', 'pure vanilla extract', 'almond extract']                                                                                                                                |               7 | 813055           |      286009 | 2008-04-09 |        5 | don't let the calories and fat grams scare you off. This is a wonderful recipe and is perfect for the summer cook-out topped with fresh berries! It will make you proud. This is meant to be shared!                                                                                                                                             |            5 |           1 |      878.3 |          63 |     326 |       13 |        20 |             123 |              39 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  | ['time-to-make', 'course', 'main-ingredient', 'preparation', 'main-dish', 'potatoes', 'vegetables', '4-hours-or-less', 'meatloaf', 'simply-potatoes2']                                                                                                                                             | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 | ['pan fry bacon , and set aside on a paper towel to absorb excess grease', 'mince yellow onion , red bell pepper , and add to your mixing bowl', 'chop garlic and set aside', 'put 1tbsp olive oil into a saut pan , along with chopped garlic , teaspoons white pepper and a pinch of kosher salt', 'bring to a medium heat to sweat your garlic', 'preheat oven to 350f', 'coarsely chop your baby spinach add to your heated pan , stir frequently for approximately 5 min to wilt', 'add your spinach to the mixing bowl', 'chop your now cooled bacon , and add it to the mixing bowl', 'add your meatloaf mix to the bowl , with one egg and mix till thoroughly combined', 'add your goat cheese , one egg , 1 / 8 tsp white pepper and 1 / 8 tsp of kosher salt and mix till thoroughly combined', 'transfer to a 9x5 meatloaf pan , and cook for 60 min or until the internal temperature is at least 160f', 'let stand for 5min', 'melt 1tbsp unsalted butter into a frying pan , and cook up to three eggs at a time', 'crack each egg into a separate dish , in order to prevent egg shells from reaching the pan , then add salt and pepper to taste', 'wait until the egg whites are firm looking , but slightly runny on top before flipping your eggs', 'after flipping , wait 10~20 seconds before removing each egg and placing it over your slices of meatloaf'] | ready, set, cook! special edition contest entry: a mediterranean flavor inspired meatloaf dish. featuring: simply potatoes - shredded hash browns, egg, bacon, spinach, red bell pepper, and goat cheese.                                                                                                                                                                         | ['meatloaf mixture', 'unsmoked bacon', 'goat cheese', 'unsalted butter', 'eggs', 'baby spinach', 'yellow onion', 'red bell pepper', 'simply potatoes shredded hash browns', 'fresh garlic', 'kosher salt', 'white pepper', 'olive oil'] |              13 |      2.20436e+06 |      475785 | 2012-03-07 |        5 | Delicious!!!!! -- the goat cheese made the difference.  My new favorite meatloaf.                                                                                                                                                                                                                                                                |            5 |           2 |      267   |          30 |      12 |       12 |        29 |              48 |               2 |


<iframe
  src="assets/cal_distribution.html"
  width="1500"
  height="500"
  frameborder="0"
></iframe>

The distribution of calories across recipes shows a right-skewed pattern, with most recipes containing moderate calorie counts. This aligns with what we might expect from real-world recipes, where extreme calorie counts are less common.

### Key Relationships

Our analysis revealed strong correlations between calories and other nutritional components:

<iframe
  src="assets/fat_calories.html"
  width="1500"
  height="500"
  frameborder="0"
></iframe>

The scatter plot demonstrates a strong positive correlation between total fat content and calories, suggesting that fat content is a key predictor of a recipe's calorie count.


### Nutritional Analysis by Calorie Content

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

We formulated our analysis as a regression problem to predict recipe calorie content from other nutritional and recipe characteristics.

### Problem Statement
- **Task Type**: Regression (predicting a continuous numerical value)
- **Response Variable**: Calories per serving
- **Features Available at Prediction Time**: 
 - Recipe characteristics (number of steps, number of ingredients)
 - Other nutritional information (fats, proteins, carbohydrates, etc.)
 - Note: We can't use ratings or reviews as features since these would only be available after a recipe is published

### Why Predict Calories?
We chose calories as our prediction target because:
1. It's a critical nutritional metric that directly impacts meal planning and dietary decisions
2. Accurate calorie information is essential for recipe websites and health-focused applications
3. Unlike subjective measures like ratings, calories can be objectively measured and validated

### Evaluation Metric Choice
We selected Mean Absolute Error (MAE) as our evaluation metric over alternatives like MSE/RMSE for several reasons:

1. **Interpretability**: MAE represents the average number of calories our predictions are off by, making it directly meaningful to users. For example, "our predictions are off by an average of 77 calories" is more interpretable than a squared error value.

2. **Robustness**: Recipe calories naturally have some outliers (very light snacks vs. heavy meals). MAE is less sensitive to these outliers compared to squared error metrics, providing a more reliable measure of typical model performance.

3. **Linear Scale**: The impact of calorie prediction errors is relatively linear - being off by 100 calories isn't necessarily 4 times worse than being off by 50 calories. This matches MAE's linear error scale better than MSE's quadratic scale.

Alternative metrics we considered but didn't choose:
- Mean Squared Error (MSE): Would penalize large errors too heavily relative to their practical impact
- Root Mean Squared Error (RMSE): While in the same units as calories, still overemphasizes large errors
- Mean Percentage Error (MPE): Could be misleading for low-calorie recipes where small absolute errors would appear as large percentages

## Baseline Model

Our baseline model aims to predict recipe calories using just two fundamental nutritional features:

### Features
We selected two quantitative features for our initial model:
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
4. Captures the strongest correlations we observed in our exploratory analysis (total_fat correlation: 0.85, protein correlation: 0.67)

Limitations:
1. Ignores other potentially useful nutritional information (carbohydrates, sugar, etc.)
2. Doesn't account for recipe complexity (number of steps, ingredients)
3. Doesn't consider interactions between nutritional components

While the baseline model provides a reasonable starting point, there's clear room for improvement by incorporating additional features and more sophisticated modeling techniques in our final model.

## Final Model

### Feature Engineering Rationale
Building upon our baseline model's total fat and protein features, we added several additional features chosen based on nutritional science and cooking principles:

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
We selected Lasso regression as our final model for several reasons:
- Performs automatic feature selection through L1 regularization
- Maintains interpretability of coefficients
- Can handle correlated features (like our various nutritional ratios)

### Hyperparameter Tuning
- Used GridSearchCV with 5-fold cross-validation
- Tuned Lasso's alpha parameter across 50 values from 10^-4 to 1
- Best alpha = 1.0000, indicating strong regularization was beneficial
- This suggests some features were less important for prediction

### Model Performance
The final model achieved an MAE of 10.75 calories, compared to the baseline's 77.35 calories - an 86% improvement. 

Key insights from feature coefficients:
1. Carbohydrates (11.54), total fat (5.59), and protein (2.21) emerged as the strongest predictors
2. Sugar and number of ingredients had minimal impact (coefficients near zero)
3. Some engineered features showed negative coefficients, suggesting they help correct overestimates from the primary nutritional features

This substantial improvement validates our feature engineering approach based on nutritional science, though the strong regularization suggests some engineered features were less useful than theoretically expected.