# What Makes a Recipe Shine?

by Tingyi Xiao(tingyix@umich.edu)

Note: This is the final project for EECS 398 at U-M about recipes-and-rating

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

### Data Cleaning

To ensure accurate and meaningful analysis, we followed several data cleaning steps, considering how the data was collected and potential issues that could arise from user input or system defaults.

#### Handling Unrealistic Cooking Times
- **Replacing Zero Cooking Times**: The `recipes` dataset contained some entries with a `minutes` value of 0, which likely resulted from data entry errors or default values. We replaced these 0 values with `NaN` using `replace(0, np.nan)`. This ensures that recipes with invalid cooking times are excluded from analysis.
  
- **Filtering Out Extreme Cooking Times**: We filtered the dataset to exclude any recipes with cooking times exceeding 1000 minutes, as these values are likely outliers and would distort the analysis.

#### Handling Missing Values in Recipes Dataset
- **Missing Minutes and Ingredients**: We dropped any rows where the `minutes` or `n_ingredients` columns were missing, ensuring that only recipes with valid data for both features were included in our analysis.
  
- **Missing Descriptions**: We also dropped recipes with missing descriptions (`description` column), as the lack of descriptions likely indicates incomplete or uninformative entries for analysis.

#### Handling Missing Ratings in Interactions Dataset
- **Replacing Zero Ratings**: In the `interactions` dataset, we found that some recipes had a rating of 0. Since a rating of 0 likely represents invalid or placeholder data, we replaced these with `NaN` to treat them as missing values.
  
- **Converting Dates to Datetime**: The `date` column in the `interactions` dataset was originally stored as text. We converted it to `datetime` format using `pd.to_datetime()`, allowing for any future analysis that might involve temporal patterns. Invalid or poorly formatted date entries were converted to `NaT` (Not a Time).

- **Dropping Missing Ratings**: We also removed rows from the `interactions` dataset where the `rating` was missing, ensuring that only valid reviews with ratings were included.

#### Merging Datasets
To combine the recipe data with the user ratings, we performed a **left join** between the `recipes` and `interactions` datasets, using the `recipe_id` from `interactions` and the `id` from `recipes` as the key. This merge allows us to analyze how different recipe characteristics (such as `minutes`, `n_ingredients`, and `description`) relate to user ratings.

#### Final Cleaning and Dataframe Creation
After merging the datasets, we performed one final cleaning step to ensure that only rows with valid data for critical columns (`minutes`, `n_ingredients`, and `rating`) remained. This ensures that our final dataset (`clean_df`) is ready for modeling and analysis.

### Impact of Data Cleaning on Analysis:
These cleaning steps ensured that the dataset used for analysis is consistent and accurate. By removing unrealistic cooking times, handling missing ratings and descriptions, and merging datasets carefully, we created a clean dataset that accurately represents recipe and rating data, providing a solid foundation for feature engineering and model training.

### Univariate Analysis

To understand general trends in the dataset, we began with univariate analysis of key features.

**Distribution of Recipe Ratings**

We plotted the distribution of user ratings across all recipes:

<iframe
 src="assets/fig_rating.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>
 
Most users rated recipes very highly — particularly around 5 stars — which suggests a positive bias in user reviews. This trend implies that distinguishing truly exceptional recipes may require comparing slight differences within a narrow high range, making subtle predictors more valuable in our modeling.

**Distribution of Number of Ingredients**

Next, we examined how many ingredients recipes typically use:

<iframe
 src="assets/fig_ing.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

We observed that most recipes use fewer than 15 ingredients, with a steep drop-off after that point. This suggests that shorter ingredient lists are common, and may be a baseline for what users expect. Understanding how the number of ingredients relates to average ratings will help us determine if recipe complexity plays a role in user preferences.

### Bivariate Analysis

To investigate how different features influence recipe ratings, we analyzed the relationship between key variables using Plotly visualizations.

**Rating vs. Ingredient Count**

We grouped recipes based on the number of ingredients and compared their ratings using a boxplot:

<iframe
 src="assets/fig.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

This plot shows that recipes with varying numbers of ingredients generally receive similar ratings. The boxplot indicates that while the number of ingredients does not drastically affect the ratings, there is some fluctuation in the middle quartiles, suggesting that complexity in ingredients may not be a major factor in rating variations.

**Rating vs. Log(Cooking Time)**

We also examined how cooking time (log-transformed) relates to average rating:

<iframe
 src="assets/fig2.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

We observe a a weak downward trend, with most recipes clustering around moderate cooking times and ratings. This suggests that longer cooking times do not necessarily lead to higher ratings. Interestingly, very long recipes tend to receive slightly higher ratings, which could be due to a preference for more complex, time-consuming dishes among certain users.

### Interesting Aggregates

To further explore the role of ingredient count, we grouped recipes by the number of ingredients and calculated their **average rating**:

|   n_ingredients |   rating |
|----------------:|---------:|
|               5 |  4.71023 |
|               6 |  4.6782  |
|               7 |  4.66876 |
|               8 |  4.66273 |
|               9 |  4.66402 |
|              10 |  4.66181 |
|              11 |  4.68238 |
|              12 |  4.67681 |
|              13 |  4.68916 |
|              14 |  4.65737 |

We also visualized this trend using a **line plot**:

<iframe
 src="assets/fig_grouped.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

This analysis confirms Recipes with **more than 30 ingredients** tend to receive the highest average ratings, which may seem counterintuitive at first, as simpler recipes typically receive more attention. However, this could indicate that more complex recipes, which might require more effort, tend to attract a certain group of users who appreciate detailed and elaborate cooking processes.

This finding suggests that a **balance in recipe complexity**—whether in terms of ingredients or preparation time—might appeal more to users, with both simple and highly intricate recipes receiving higher ratings. Moderate complexity may strike the right chord for a broader audience.

### Imputation

We observed that some recipes had unrealistically low cooking times (e.g., 0 or 1 minute). These values were likely due to missing or incorrect data entries. To address this, we replaced these values with `NaN` and imputed them using the **median cooking time**, a robust measure less affected by outliers.

The histograms below compare the distribution of cooking times before and after imputation. While the graphs appear quite similar, the post-imputation distribution is smoother and more realistic, helping avoid the skew caused by implausibly low values. This similarity in the graphs indicates that the imputation process did not significantly alter the overall distribution, but instead helped correct the unrealistic values, preserving the integrity of the data.

**Before Imputation:**

<iframe
 src="assets/Before Imputation.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

**After Imputation:**

<iframe
 src="assets/After Imputation.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>



## Framing a Prediction Problem

We formulated a **regression problem** to **predict the average rating** a recipe will receive, using only information available at the time the recipe is submitted. This ensures that our prediction process reflects a realistic scenario—no future interactions or reviews are used in training the model.

### Response Variable  
Our target variable is **`rating`**, specifically the rating given by users. We aimed to predict it based on features known at submission time:  
- `minutes` (cooking time)  
- `n_steps` (number of steps)  
- `n_ingredients` (number of ingredients, computed from the ingredient list)  
- `year` (extracted from the `submitted` date to capture potential trends over time)

We excluded any post-submission data (e.g., reviews, ratings from other users, etc.) to preserve causality.

### Evaluation Metric  
We used **Mean Absolute Error (MAE)** to evaluate our models. MAE is easy to interpret (it tells us, on average, how far our predictions are from the true values) and is more robust to outliers than squared-error metrics.

### Results  
We trained two models as an initial comparison:  

- **Linear Regression** (baseline): MAE = **0.497**  
- **Random Forest Regressor**: MAE = **0.478**  

The Random Forest model outperformed the linear baseline, indicating that **non-linear patterns** exist between recipe features and user ratings.

### Next Steps  
Future improvements may include experimenting with additional features like recipe tags, natural language processing of the description field, or clustering recipes by cuisine type.

## Baseline Model

For our baseline, we used a **Linear Regression model** to predict the average recipe rating. The purpose of this model is to establish a simple benchmark against which we can compare more complex models later.

### Features and Their Types  
We included the following features in our baseline model:

- **`minutes`** (quantitative): Total preparation time  
- **`n_steps`** (quantitative): Number of steps in the recipe  
- **`n_ingredients`** (quantitative): Number of ingredients used  

There were no ordinal or nominal features in this model, so no categorical encoding was necessary.

### Preprocessing  
Before fitting the model, we standardized all numeric features using **`StandardScaler`** to ensure they were on comparable scales. This is important for linear models, as features on larger scales can disproportionately affect the regression weights.

### Performance  
We trained the model on 80% of the data and tested it on the remaining 20%, using **Mean Absolute Error (MAE)** as our evaluation metric.

- **Baseline Model MAE: 0.498**

This MAE reflects the average absolute difference between predicted and actual ratings. While not highly accurate, the model serves its purpose as a simple benchmark. The relatively straightforward structure and few input features help us interpret the results and compare against more advanced approaches like Random Forests.

### Conclusion  
This baseline model is **not expected to be highly predictive**, but it provides a useful reference point. Any future models that incorporate non-linear relationships, additional features, or interaction terms should ideally outperform this model in terms of MAE.


## Final Model

To improve prediction performance over our baseline, we engineered new features based on domain knowledge and used a **Random Forest Regressor** with hyperparameter tuning via cross-validation.

###  Feature Engineering

We added three features that better capture recipe complexity and non-linear patterns:

- **`log_minutes`**:  
  Cooking time (`minutes`) is right-skewed. Taking the log (`log1p`) reduces this skew, making the model less sensitive to extremely long recipes and improving generalization.

- **`ingredient_bin`**:  
  Based on earlier visualizations, we saw that recipes with 6–15 ingredients tend to receive higher ratings. Binning `n_ingredients` into intervals (e.g., ‘0–5’, ‘6–10’, etc.) allows us to represent this non-linear relationship effectively.

- **`minutes_n_steps`** (interaction feature):  
  We created a new feature that multiplies `minutes` by `n_steps` to represent overall recipe complexity. A recipe that takes both a long time and many steps might impact user perception and ratings more than either feature alone.

### Modeling and Hyperparameter Tuning

We used a **`RandomForestRegressor`** because it's well-suited for capturing non-linear relationships and interactions between features. We performed a grid search over the following hyperparameters:

- `n_estimators`: number of trees in the forest (`[50, 100, 200]`)
- `max_depth`: maximum depth of the trees (`[None, 10, 20]`)
- `min_samples_split`: minimum samples required to split a node (`[2, 5, 10]`)

Hyperparameters were selected using 5-fold cross-validation with **Mean Absolute Error (MAE)** as the scoring metric.

> **Best Hyperparameters:**  
> `n_estimators = 200`, `max_depth = None`, `min_samples_split = 2`

### Model Performance

| Model             | MAE    |
|------------------|--------|
| Baseline (Linear Regression) | 0.498 |
| Final Model (Random Forest) | **0.491** |

The final model achieves a **MAE of 0.491**, showing a modest but meaningful improvement over the baseline model. This indicates that the additional features and the more flexible model architecture help better capture user preferences based on recipe characteristics.
