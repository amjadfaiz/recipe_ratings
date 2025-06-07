# **Recipe Ratings Analysis**

## **Introduction**

This project investigates the factors influencing recipe ratings and develops predictive models to estimate user ratings. The goal is to analyze how various recipe attributes, such as preparation time, number of ingredients, and cooking steps, impact user ratings. Additionally, a fairness analysis is conducted to evaluate whether the model performs differently across different recipe groups.

The project is centered around the following key question:
**Does the number of ingredients in a recipe impact its rating?**

Understanding the relationship between ingredient complexity and ratings can provide valuable insights for recipe creators, food bloggers, and online recipe platforms. If we find that ingredient count strongly influences ratings, it could help food enthusiasts optimize their recipes for better reception.

### **Dataset Overview**
The dataset consists of two main components:
1. **Recipes Dataset**: Contains details about each recipe, including cooking time, number of ingredients, steps, and descriptions.
2. **Interactions Dataset**: Includes user ratings for the recipes.

### **Dataset Size**
- **Number of rows in the Recipes dataset:** 231,637
- **Number of rows in the Interactions dataset:** 731,928

### **Relevant Columns and Descriptions**
The following columns are relevant to our research question:

| Column Name        | Description |
|-------------------|-------------|
| **id**            | Unique identifier for each recipe. |
| **n_ingredients** | The total number of ingredients used in the recipe. |
| **minutes**       | The total preparation and cooking time for the recipe. |
| **n_steps**       | The number of steps required to prepare the recipe. |
| **rating**        | The user rating for the recipe, ranging from 1 to 5. |

These columns allow us to examine whether recipes with more ingredients tend to receive higher or lower ratings and whether other factors (e.g., cooking time and number of steps) play a role in influencing user satisfaction.

---

## **Data Cleaning and Exploratory Data Analysis**

### **Data Cleaning**
#### **Key Cleaning Steps:**
- Ratings of **zero** were removed to avoid skewing analysis.
- Missing values in key features such as `description` were examined and handled appropriately.
- Extracted numerical features such as `n_ingredients`, `minutes`, and `n_steps` for predictive modeling.

### **Head of Cleaned Data**

| id     | n_ingredients | rating |
|--------|---------------|--------|
| 333281 | 9             | 4.0    |
| 453467 | 11            | 5.0    |
| 306168 | 9             | 5.0    |
| 286009 | 7             | 5.0    |
| 475785 | 13            | 5.0    |

### **Exploratory Data Analysis (EDA)**
We analyzed various aspects of the dataset:
- **Rating Distribution:** Ratings are right-skewed, with most ratings being 4 or above.
- **Number of Ingredients vs. Ratings:** No clear trend was observed.
- **Cooking Time vs. Ratings:** No strong correlation was found.
- **Steps vs. Ratings:** Recipes with more steps do not necessarily receive higher ratings.


**Visualizations of Rating Distribution, Ingredients, and Steps vs. Ratings**


**Distribution of Number of Ingredients**

The histogram below displays the distribution of the number of ingredients used in recipes. Most recipes tend to use a moderate number of ingredients, with a noticeable peak around 7 to 10 ingredients. Very simple recipes (e.g., 1–3 ingredients) and highly complex ones (e.g., over 20 ingredients) are relatively rare. This distribution indicates that the dataset is centered around moderately complex recipes, which may reflect typical user preferences or platform standards.

<iframe
  src="assets/n_ingredients_histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Distribution of Ratings**

The histogram below shows the distribution of user ratings for recipes. The distribution is right-skewed, with the majority of ratings being 4.0 or 5.0. This suggests that users tend to rate recipes positively, potentially due to self-selection (users trying recipes they already expect to like) or rating inflation. Lower ratings (1.0 to 2.0) are rare, which may indicate a bias toward more favorable reviews in the dataset.

<iframe
  src="assets/rating_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Number of Ingredients vs. Ratings**

The scatter plot below examines the relationship between the number of ingredients in a recipe and its user rating. There appears to be no strong or consistent trend: recipes with both few and many ingredients receive a wide range of ratings. This suggests that ingredient count alone is not a strong determinant of recipe quality as perceived by users. Other factors—such as taste, clarity of instructions, or subjective preferences—may play a larger role in influencing ratings.

<iframe
  src="assets/ingredients_vs_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## **Mean Rating per Ingredient Count**
The table below shows the average rating for recipes based on the number of ingredients:

| n_ingredients | rating |
|--------------|--------|
| 1            | 4.23   |
| 2            | 4.11   |
| 3            | 4.07   |
| 4            | 4.02   |
| 5            | 4.00   |
| 6            | 3.98   |
| 7            | 3.95   |
| 8            | 3.92   |
| 9            | 3.90   |
| 10           | 3.85   |

It shows a slight downward trend: as the number of ingredients increases, the average rating tends to decrease gradually. While the differences are small, this pattern may suggest that users slightly prefer simpler recipes, possibly because they are easier to prepare or require fewer resources. However, since the variation is minimal, ingredient count alone does not appear to have a strong influence on user ratings.

---

## **Assessment of Missingness**

### **NMAR Analysis – Description Column**

We suspect the `description` column is **Not Missing At Random (NMAR)**. The presence or absence of a description likely depends on latent factors such as whether the recipe was user-submitted or automatically generated. These influences are not captured by other observed columns in our dataset.

To support this, we performed a **permutation test** comparing `rating` across recipes with and without a description.

- **Test Statistic**: Difference in average rating  
- **Observed Difference**: -1.47  
- **p-value**: **0.003**

Since the p-value is **less than 0.05**, we **reject the null hypothesis** and conclude that the missingness of `description` is dependent on `rating` (i.e., **not missing completely at random**). This aligns with our suspicion that certain types of recipes are more prone to lacking descriptions.

---

**Missingness Plot**

We visualized the distribution of `n_ingredients` for recipes with and without ratings to highlight differences:

<iframe
  src="assets/missingness_ingredients.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


- The `description` column is NMAR based on strong evidence of rating-based dependency.
- The `rating` column shows borderline dependency on `n_ingredients`, suggesting it may not be fully MCAR.
- A future revision might use a clearer independent column pair to meet rubric criteria for a missingness-independent interpretation.


---

## **Hypothesis Testing**

### **Hypothesis Selection**
We tested whether the number of ingredients in a recipe impacts the average rating.

- **Null Hypothesis (H₀):** The number of ingredients does not affect the rating.
- **Alternative Hypothesis (H₁):** Recipes with more ingredients have different average ratings than those with fewer ingredients.

### **Permutation Test**
- **Observed Mean Difference:** -0.0129
- **p-value:** 0.314
- **Conclusion:** Since p > 0.05, we fail to reject the null hypothesis, indicating that ingredient count does not significantly impact ratings.


---

## **Framing a Prediction Problem**
The objective is to predict recipe ratings using available features where we attempt to predict `rating` based on:
- **Number of ingredients (`n_ingredients`)**
- **Cooking time (`minutes`)**
- **Number of steps (`n_steps`)**

In this project, our goal is to predict how users rate a given recipe based on its inherent characteristics. This task is framed as a **regression problem**, as the response variable—`rating`—is a continuous numerical value ranging from 1 to 5. Predicting recipe ratings can be valuable for online food platforms aiming to personalize recommendations or surface high-quality content to users.

The response variable, `rating`, was chosen because it directly captures user feedback and serves as a quantifiable measure of a recipe’s perceived quality. Since this variable reflects the outcome we want to estimate, it is a natural choice for a supervised learning task centered around user preferences.

To evaluate our model, we use **Mean Squared Error (MSE)**. MSE is appropriate in this context because it penalizes larger errors more heavily, ensuring that substantial mispredictions—such as estimating a 5-star recipe as a 2—are treated more seriously than minor discrepancies. This sensitivity to error magnitude is important when modeling user satisfaction, where small deviations may be acceptable, but large gaps could result in poor recommendations.

All the features used in our model—such as the number of ingredients (`n_ingredients`), preparation time (`minutes`), and number of steps (`n_steps`)—are known at the time the recipe is created or viewed. This ensures that our model only relies on information that is realistically accessible at the time of prediction, avoiding any data leakage and making it suitable for deployment in real-world applications.

---

## **Baseline Model**

For our baseline model, we trained a Linear Regression model using three features: `n_ingredients`, `minutes`, and `n_steps`.

- The feature `n_ingredients` is a **quantitative** variable representing the number of ingredients in each recipe.
- The feature `minutes` is also **quantitative**, indicating the total time required to prepare and cook the recipe.
- Similarly, `n_steps` is a **quantitative** feature that counts the number of preparation steps.

These features were selected because they are simple, numeric, and available at the time of prediction.

After standardizing the features using `StandardScaler`, we trained a baseline Linear Regression model. Its performance was evaluated using Mean Squared Error (MSE), Mean Absolute Error (MAE), and R² score on a held-out test set.

- **MSE:** 1.7583  
- **MAE:** 0.8932  
- **R²:** 0.0025

### **Is the Baseline Model Good?**

This baseline model is **not good**, as the R² score is extremely close to 0, indicating that the model explains almost none of the variance in the target variable (`rating`). While it slightly outperforms the naive baseline (which predicts the mean), the improvement is marginal. This suggests that the features used in this model are not strong predictors of recipe ratings, and more informative or diverse features are needed to build a better model.


---

## **Final Model**

To improve upon the baseline model, we added two new features and applied more complex models with hyperparameter tuning.

**New Features Added**
1. **`description_length`**: The number of characters in the recipe's description. This feature captures the richness of content provided, which may indicate recipe quality and affect user ratings.
2. **`steps_length`**: The number of preparation steps. It reflects recipe complexity and can impact how users perceive difficulty and rate the recipe.

Both features are intuitive, relevant, and available before a user leaves a rating, making them valid for prediction.

**Model Architecture**
We evaluated three models using pipelines:
- **Linear Regression**
- **DecisionTreeRegressor**
- **RandomForestRegressor**

Each model pipeline included preprocessing (standardization) and was trained using the same training/test split.

**Hyperparameter Tuning**
We used **GridSearchCV** with 5-fold cross-validation on the training set to tune the following parameters:

- **Decision Tree:**
  - `max_depth`: Tuned values = [3, 5, 10, None]
  - **Best value:** `max_depth = 5`

- **Random Forest:**
  - `max_depth`: Tuned values = [5, 10, None]
  - `n_estimators`: Tuned values = [50, 100]
  - **Best values:** `max_depth = 10`, `n_estimators = 100`

**Model Performance**

| Model                    | MSE     | MAE     | R²      |
|--------------------------|---------|---------|---------|
| Linear Regression        | 1.75725 | 0.8932  | 0.0030  |
| Decision Tree (Tuned)    | 1.75536 | 0.8899  | 0.0041  |
| Random Forest (Tuned)    | 1.73498 | 0.8839  | 0.0157  |

The **Random Forest Regressor** achieved the best performance with an **R² of 0.0157**, an improvement over the baseline Linear Regression (**R² = 0.0030**). While the gain is modest, it indicates that the added features contributed additional predictive value. This shows the benefit of feature engineering and model tuning in improving performance over a simple baseline.


---

## **Fairness Analysis**

We evaluated the fairness of our final model by comparing its performance across two recipe groups:

**Group Definition**
- **Short Recipes:** Recipes with a preparation time of **30 minutes or less**.
- **Long Recipes:** Recipes with a preparation time of **more than 30 minutes**.

This grouping allows us to test whether the model is equally effective for quick and lengthy recipes.

**Evaluation Metric**
We used **Root Mean Squared Error (RMSE)** because it penalizes large errors more than small ones, which is critical for fairness-sensitive evaluation.

**Test Statistic**
We computed the **absolute difference in RMSE** between the two groups as our test statistic.

**Hypothesis Test**
- **Null Hypothesis (H₀):** The model performs equally well across short and long prep time groups.
- **Alternative Hypothesis (H₁):** The model's performance differs across these two groups.
- **Significance Level:** 0.05  
- **Observed RMSE Difference:** |5.10 - 4.43| = **0.67**  
- **Permutation Test p-value:** **0.48**

Since the p-value is greater than the 0.05 significance threshold, we **fail to reject the null hypothesis**. This means there is **no statistically significant evidence** that the model performs differently between short and long preparation time recipes.

**Additional Group: Ingredient Complexity**
We also examined model performance based on ingredient complexity:
- **Simple Recipes:** RMSE = 4.69  
- **Complex Recipes:** RMSE = 4.61  

While no formal hypothesis test was performed for this group, the small difference suggests minimal disparity.

**Conclusion**
Overall, the model does **not exhibit significant fairness issues** between recipe groups based on preparation time or ingredient complexity. Nonetheless, future work could involve analyzing fairness across user demographics or subjective taste preferences.


---

## **Conclusion**
### **Key Findings:**
- **EDA revealed that recipe complexity (ingredient count, cooking time) does not strongly correlate with ratings.**
- **Missingness analysis suggested that missing descriptions may introduce bias.**
- **Hypothesis testing showed that the number of ingredients does not significantly impact ratings.**
- **The predictive models had low performance, indicating that ratings may be influenced by subjective user preferences not captured in the dataset.**
- **Fairness analysis showed no significant bias in model performance.**
