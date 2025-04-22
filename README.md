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

'recipe_id': Recipe ID 

'date': Date of interaction

'rating': Rating given

Using these datasets, we will analyze patterns to determine what factors contribute to a higher average rating. Are recipes with fewer steps more likely to be highly rated? Does the preparation time affect the rating? Do specific tags or nutritional content correlate with higher ratings?

These insights are useful for home chefs, food bloggers, and anyone looking to understand trends in the culinary world, helping them create recipes that are more likely to earn top ratings.

## Data Cleaning and Exploratory Data Analysis

To ensure a meaningful and accurate analysis, we carefully cleaned and prepared the data, taking into account how the data was generated and the potential issues that might arise from user input or system defaults.

1. **Converted Timestamps**  
   The `date` column in the `RAW_interactions` dataset was stored as text. Since users submitted reviews over time, we converted this column into proper datetime format to allow for any future temporal analysis.  
   `interactions['date'] = pd.to_datetime(interactions['date'], errors='coerce')`

2. **Removed Unrealistic Preparation Times**  
   Some recipes reported a preparation time of 0 minutes, which likely results from data entry errors or default values. We replaced `minutes = 0` with `NaN` and later removed those rows, along with extremely high values (greater than 1000 minutes) that could skew the analysis.  
   `recipes['minutes'] = recipes['minutes'].replace(0, pd.NA)`  
   `recipes = recipes[recipes['minutes'] <= 1000]`

3. **Handled Missing Values**  
   - We dropped recipes that lacked a description, assuming that these entries are incomplete or less informative.  
     `recipes.dropna(subset=['description'], inplace=True)`
   - We kept reviews without text (`review` column) because our prediction focuses on the numerical `rating` column, which was present in most cases.  
   - Ratings of 0 were treated as missing, since they likely indicate invalid or placeholder entries.  
     `merged_df['rating'] = merged_df['rating'].replace(0, np.nan)`

4. **Created a Feature: Number of Ingredients**  
   Although not shown in the snippet above, we parsed the ingredient list to count how many ingredients each recipe included, adding a new `num_ingredients` column. This allows us to explore whether simpler recipes (with fewer ingredients) are rated more highly.

5. **Merged Datasets**  
   Finally, we joined the two datasets on the recipe ID to combine recipe features with user ratings. This merged dataset allows us to analyze patterns between recipe characteristics and their average ratings.  
   `merged_df = pd.merge(interactions, recipes, how='left', left_on='recipe_id', right_on='id')`

6. **Created Average Rating per Recipe**  
   To better understand the overall popularity of each recipe, we calculated the average rating each recipe received and added it as a new column.  
   `avg_rating_per_recipe = merged_df.groupby('id')['rating'].mean()`  
   `merged_df = merged_df.merge(avg_rating_per_recipe.rename('avg_rating'), on='id', how='left')`

To understand general trends in the dataset, we began with univariate analysis of key features.

**Distribution of Recipe Ratings**
We plotted the distribution of user ratings across all recipes:

(embed fig_rating plot here)

Most users rated recipes very highly — particularly around 5 stars — which suggests a positive bias in user reviews. This trend implies that distinguishing truly exceptional recipes may require comparing slight differences within a narrow high range, making subtle predictors more valuable in our modeling.

**Distribution of Number of Ingredients**
Next, we examined how many ingredients recipes typically use:

(embed fig_ing plot here)

We observed that most recipes use fewer than 15 ingredients, with a steep drop-off after that point. This suggests that shorter ingredient lists are common, and may be a baseline for what users expect. Understanding how the number of ingredients relates to average ratings will help us determine if recipe complexity plays a role in user preferences.

To investigate how different features influence recipe ratings, we analyzed the relationship between key variables using Plotly visualizations.

**Rating vs. Ingredient Count**
We grouped recipes based on the number of ingredients and compared their ratings using a boxplot:

(embed the fig Plotly boxplot here)

This plot shows that recipes with a moderate number of ingredients (between 6–15) tend to receive slightly higher ratings. In contrast, recipes with either very few or many ingredients show greater variability and slightly lower median ratings. This suggests that recipes perceived as balanced in complexity may be more appealing to users.

**Rating vs. Log(Cooking Time)**
We also examined how cooking time (log-transformed) relates to average rating:

(embed the fig2 Plotly scatter plot here)

We observe a weak downward trend: recipes with shorter cooking times tend to have slightly higher ratings. Most highly rated recipes cluster at lower cooking times, while those with longer durations show more variability or lower ratings. This suggests that users might prefer quicker recipes, potentially due to convenience or time constraints.

To further explore the role of ingredient count, we grouped recipes by the number of ingredients and calculated their **average rating**:
(Replace this sample table with actual values from your code output)

We also visualized this trend using a **line plot**:

(Embed fig_grouped Plotly line chart here)

This analysis confirms that recipes with a moderate number of ingredients (about 6 to 10) tend to receive the highest average ratings. Recipes that are either too simple or too complex may be less appealing to users. This supports the idea that users value a balance of effort and reward in their cooking experiences.
