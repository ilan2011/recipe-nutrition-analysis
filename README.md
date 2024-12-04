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


<iframe
  src="assets/cal_distribution.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The distribution of calories across recipes shows a right-skewed pattern, with most recipes containing moderate calorie counts. This aligns with what we might expect from real-world recipes, where extreme calorie counts are less common.


The distribution of calories across recipes shows a right-skewed pattern, with most recipes containing moderate calorie counts. This aligns with what we might expect from real-world recipes, where extreme calorie counts are less common.


### Nutritional Analysis by Calorie Content

Here's how nutritional values and recipe characteristics vary across calorie quintiles:

| Calorie Percentile | Total Fat (PDV) | Protein (PDV) | Carbs (PDV) | Sugar (PDV) | Sodium (PDV) | Saturated Fat (PDV) | Avg Ingredients | Avg Steps |
|-------------------|-----------------|---------------|-------------|-------------|--------------|-------------------|----------------|------------|
| 0-20%             | 5.06           | 4.87          | 3.08        | 20.61       | 7.88         | 5.86              | 7.29           | 7.85       |
| 20-40%            | 11.98          | 14.04         | 6.66        | 34.91       | 12.64        | 14.29             | 8.44           | 8.94       |
| 40-60%            | 19.69          | 25.74         | 9.19        | 41.56       | 19.28        | 24.07             | 9.20           | 9.75       |
| 60-80%            | 30.25          | 37.95         | 12.44       | 51.03       | 26.04        | 38.79             | 9.82           | 10.78      |
| 80-100%           | 56.60          | 59.91         | 18.13       | 66.15       | 39.25        | 69.37             | 10.57          | 12.07      |

This table reveals several interesting patterns:
- As calories increase, all nutritional components tend to increase proportionally
- Higher-calorie recipes tend to be more complex, with more ingredients and steps
- The jump in nutritional values is particularly dramatic in the highest calorie quintile (80-100%)
- Sugar content shows the smallest relative increase across quintiles compared to other nutrients