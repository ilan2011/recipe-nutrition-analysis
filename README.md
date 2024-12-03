# Recipe Nutrition Analysis

**By**: Ilan Breines  
**Contact**: ilanbrei@umich.edu

## Introduction

Can we predict a recipe's calorie content based on its other nutritional information and characteristics? This analysis explores recipe data from Food.com to understand the relationships between different nutritional components and predict calorie content.

The dataset contains information from two main sources:
- RAW_recipes.csv: Contains recipe details including nutritional information, preparation time, and steps
- RAW_interactions.csv: Contains user ratings and reviews

After merging and cleaning, our analysis covers a comprehensive set of recipes with detailed nutritional profiles, including:
- Calories
- Total fat (PDV - Percentage of Daily Value)
- Sugar (PDV)
- Sodium (PDV)
- Protein (PDV)
- Saturated fat (PDV)
- Carbohydrates (PDV)

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning Process
1. Merged recipe and rating data using a left join to preserve all recipes
2. Replaced ratings of 0 with NaN values, as these likely represent missing ratings rather than actual zero ratings
3. Calculated average rating per recipe
4. Extracted nutritional information from string format into separate columns
5. Removed outliers:
   - Filtered cooking times to between 1st and 99th percentiles
   - Removed extreme nutritional values above 99th percentile



The distribution of calories across recipes shows a right-skewed pattern, with most recipes containing moderate calorie counts. This aligns with what we might expect from real-world recipes, where extreme calorie counts are less common.


### Nutritional Analysis by Calorie Content

Here's how other nutritional values vary across calorie quintiles:

| Calorie Range | Avg Fat (PDV) | Avg Protein (PDV) | Avg Carbs (PDV) | Avg Steps |
|---------------|---------------|-------------------|-----------------|-----------|
| 0-20%         | 12.2         | 8.5              | 15.3           | 6.8       |
| 20-40%        | 18.7         | 12.3             | 22.1           | 7.2       |
| 40-60%        | 25.4         | 15.8             | 28.6           | 7.8       |
| 60-80%        | 34.2         | 19.7             | 35.9           | 8.3       |
| 80-100%       | 48.6         | 25.4             | 45.2           | 8.9       |
