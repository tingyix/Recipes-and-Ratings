# Recipes-and-Ratings
## Introduction
Have you ever wondered what makes a recipe truly outstanding? In the age of online cooking platforms, thousands of recipes are rated and reviewed daily, providing a rich dataset to explore. This project seeks to answer the question: **“What kinds of recipes get higher average ratings?”**

To explore this question, we use two datasets:
**RAW_recipes**: This dataset contains **83,782** rows and **12** columns, detailing individual recipes. Relevant columns for our analysis include:
'id': Recipe ID
'minutes': Minutes to prepare recipe
'submitted': Date recipe was submitted
'n_steps': Number of steps in recipe
'description': User-provided description
'n_ingredients': Number of ingredients in recipe

**RAW_interactions**: This dataset contains **731,927** rows and **5** columns, capturing user interactions with the recipes. Relevant columns include:
'recipe_id': The ID of the recipe being rated.
'date': Date of interaction
'rating': Rating given

Using these datasets, we will analyze patterns to determine what factors contribute to a higher average rating. Are recipes with fewer steps more likely to be highly rated? Does the preparation time affect the rating? Do specific tags or nutritional content correlate with higher ratings?

These insights are useful for home chefs, food bloggers, and anyone looking to understand trends in the culinary world, helping them create recipes that are more likely to earn top ratings.

