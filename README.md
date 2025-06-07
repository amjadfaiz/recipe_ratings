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
<iframe
  src="assets/n_ingredients_histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/rating_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

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


---

## **Assessment of Missingness**

### **Addressing NMAR (Not Missing At Random) Question**
We examined whether missing values in the `description` column were associated with rating differences.

### **Permutation Tests for Missingness**
A permutation test was conducted:
- **Observed Mean Difference:** -1.47
- **p-value:** 0.003
- Conclusion: The missingness in `description` is likely **not random** (NMAR). This means certain recipes are more prone to missing descriptions, which may introduce bias.

**Missingness Analysis Plot**
<iframe
  src="assets/missingness_ingredients.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
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

---

## **Baseline Model**
We trained a **Linear Regression model** as the baseline.

**Results:**
- **Mean Squared Error (MSE):** 1.7626
- **Mean Absolute Error (MAE):** 0.8935
- **R² Score:** -0.0000 (indicating the model has no predictive power)

### **Interpretation**
- The model performed poorly, meaning that the selected features are not strong predictors of ratings.
- This led to further feature engineering in the final model.


---

## **Final Model**
To improve performance, additional transformations and model selection techniques were applied:
- **Feature Engineering:** Created additional features such as text-based metrics and log transformations.
- **Hyperparameter Tuning:** Used **GridSearchCV** to optimize model parameters.
- **Models Compared:** Linear Regression, Decision Tree, Random Forest.

### **Best Model: Linear Regression (After Feature Engineering)**
- **MSE:** 1.7573
- **MAE:** 0.8932
- **R² Score:** 0.0030 (a slight improvement over the baseline but still very weak)

### **Interpretation**
- Even with additional feature engineering, the model's predictive power remained low.
- This suggests that user ratings might be influenced by **subjective factors not captured in the dataset**.


---

## **Fairness Analysis**

We analyzed model performance fairness across different groups.

### **Preparation Time Category**
- **RMSE for Short Prep Time:** 4.43
- **RMSE for Long Prep Time:** 5.10
- **Permutation Test p-value:** 0.44 (No statistical bias found)

### **Ingredient Complexity**
- **RMSE for Simple Recipes:** 4.69
- **RMSE for Complex Recipes:** 4.61

### **Conclusion**
- No significant fairness concerns were identified.
- However, a more detailed fairness analysis incorporating user biases could be explored in the future.

---

## **Conclusion**
### **Key Findings:**
- **EDA revealed that recipe complexity (ingredient count, cooking time) does not strongly correlate with ratings.**
- **Missingness analysis suggested that missing descriptions may introduce bias.**
- **Hypothesis testing showed that the number of ingredients does not significantly impact ratings.**
- **The predictive models had low performance, indicating that ratings may be influenced by subjective user preferences not captured in the dataset.**
- **Fairness analysis showed no significant bias in model performance.**
