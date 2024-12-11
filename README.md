# Cooking Up Data: A Data Science Approach to Recipe Success

By Khoi Phan (k8phan@ucsd.edu)

---

## Introduction

The world is rapidly transitioning towards an increasingly "online" state, where digital technologies are transforming how we communicate, conduct business, access information, and interact with services across various aspects of life. One of many aspect being food; it is apparent that food is becoming a mainstream topic that society is constantly discussing about, either for fitness/health purposes or for hobbies like cooking and social media content creation.

In this project, I will be focusing on two datasets relating to food recipes posted since 2008 on [food.com](https://www.food.com/). Specifically, I am trying to answer the question: **"How do the nutritional characteristics of a recipe, specifically its protein content, impact its average user rating?"**

For food recipe creators and social media food content creators, understanding how nutritional content affects ratings can help them optimize their recipes and content for higher ratings and interactions. For consumers, this insight might help them make healthier choices based on users' feedbacks and ratings. Additionally, understanding the impact of protein content could potentially help promote healthier eating habits.

For the first dataset, `recipes`, it has 83782 rows, representing the recipes that got posted and 12 columns representing the information about each recipe. A description of each column is given below.

| Column             | Description |
| :----------------- | :--------------|
| `'name'`           | Recipe name |
| `'id'`             | Recipe ID |
| `'minutes'`        | Minutes to prepare recipe |
| `'contributor_id'` | User ID who submitted this recipe |
| `'submitted'`      | Date recipe was submitted |    
| `'tags'`           | Food.com tags for recipe | 
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe |     
| `'steps'`          | Text for recipe steps, in order |  
| `'description'`    | User-provided description |   
| `'ingredients'`    | Text for recipe ingredients |    
| `'n_ingredients'`  | Number of ingredients in recipe |    

For the second dataset, `ratings`, it has 731927 rows, representing the reviews and ratings submitted for the recipes in `recipes`. A description of each column is given below.

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Cleaning the data was a crucial step in ensuring the quality and reliability of my analyses. The raw data required significant preprocessing to make it suitable for modeling. Here's a detailed explanation of the steps I took:

1. Merging Datasets
	- Process: Merged the two datasets using a left join on the recipe's ID.
	- Effect: This merge enabled me to combine recipe-specific features with user interaction data, such as ratings.

2. Handling Ratings of 0
	- Process: Replaced all ratings of 0 with NaN.
	- Effect: This prevented the use of erroneous ratings that could skew model performance and analysis.
3. Feature Transformation
	- Column: `'nutrition'`
		- Process: Splited the `'nutrition'` column into individual columns: `'calories'`, `'total fat (PDV)'`, `'sugar (PDV)'`, `'sodium (PDV)'`, `'protein (PDV)'`, `'saturated fat (PDV)'`, and `'carbs (PDV)'`.
		- Effect: Allowed for deeper nutritional analysis, specifically for deriving metrics like the proportion of calories from protein.
	- Columns: `'tags'` and `'ingredients'`
		- Process: Converted these columns from string format to lists.
		- Effect: Made these columns more structured, useful for feature extraction.
	- Columns: `'submitted'` and `'date'`
		- Process: Converted these columns to datetime format.
		- Effect: Enabled potential time-based analysis, such as trends in recipe ratings or submission patterns.
4. Creating New Columns
	- `'avg rating'`
		- Process: Calculated the average rating for each recipe based on user interactions.
		- Effect: Provided the target variable for modeling and analysis.
	- `'protein prop'`
		- Process: Calculated the proportion of calories derived from protein.
		- Effect: Enabled me to focus on high-protein recipes in alignment with my central question.
	- `'high in protein'`
		- Process: Created a binary indicator for recipes where more than 30% of calories came from protein.
		- Effect: Allowed for easy grouping of recipes into "high-protein" and "low-protein" categories.
5. Column Droppping, Renaming and Reordering
	- Process: Removed columns such as `'id'`, `'contributor_id'`, and `'description'` that were not relevant to the project’s goals. Renamed columns to improve clarity (`'prep time'`, `'date submitted`', etc.) and reordered them for easier interpretation.
	- Effect: Made the dataset more user-friendly for exploration and visualization.

The first five observations of the cleaned dataset (without the `'tags'` and `'ingredients'` columns for better visibility):

|   recipe id | name                                 | date submitted      | date interacted     |   avg rating |   prep time |   n_steps |   n_ingredients |   calories |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbs (PDV) |   rating |   protein prop | high in protein   |
|------------:|:-------------------------------------|:--------------------|:--------------------|-------------:|------------:|----------:|----------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|--------------:|---------:|---------------:|:------------------|
|      333281 | 1 brownies in the world    best ever | 2008-10-27 00:00:00 | 2008-11-19 00:00:00 |            4 |          40 |        10 |               9 |      138.4 |                10 |            50 |              3 |               3 |                    19 |             6 |        4 |      0.0433526 | False             |
|      453467 | 1 in canada chocolate chip cookies   | 2011-04-11 00:00:00 | 2012-01-26 00:00:00 |            5 |          45 |        12 |              11 |      595.1 |                46 |           211 |             22 |              13 |                    51 |            26 |        5 |      0.0436901 | False             |
|      306168 | 412 broccoli casserole               | 2008-05-30 00:00:00 | 2008-12-31 00:00:00 |            5 |          40 |         6 |               9 |      194.8 |                20 |             6 |             32 |              22 |                    36 |             3 |        5 |      0.225873  | False             |
|      306168 | 412 broccoli casserole               | 2008-05-30 00:00:00 | 2009-04-13 00:00:00 |            5 |          40 |         6 |               9 |      194.8 |                20 |             6 |             32 |              22 |                    36 |             3 |        5 |      0.225873  | False             |
|      306168 | 412 broccoli casserole               | 2008-05-30 00:00:00 | 2013-08-02 00:00:00 |            5 |          40 |         6 |               9 |      194.8 |                20 |             6 |             32 |              22 |                    36 |             3 |        5 |      0.225873  | False             |

### Univariate Analysis

In the graph below, we are examining the distribution of protein content as proportion of calories. The data follows a right-skewed distribution, indicating that most of the recipes in our dataset have very few proteins in them. Another way to interpret this result is as the proportion of protein increases, the count of recipes decreases, implying a negative correlation between the count of recipes and the proportion of protein.

<iframe
  src="assets/univariate.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>

### Bivariate Analysis

In the graph below, we are examining the count of ratings for high and low-protein recipes. We see that the majority of our dataset is considered as low-protein recipes, which aligns with our univariate analysis. Another key observation to make is that although there are few high-protein recipes, they are all mostly highly rated recipes.

<iframe
  src="assets/bivariate.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>

### Interesting Aggregates

In this table, we are examining the averages across various variables for the two categorical group of recipes, high-protein and low-protein. We first see that the average proportion of protein make sense, being that 30% is the threshold for a recipe to be considered a high-protein recipe. In addition, there are no significant difference in the average rating, the average number of ingredients used and the average prep time across the two groups. But we do see that the average calories differs quite a bit, indicating that recipes with higher protein content are estimated to be less in calories, on average.

| high in protein   |    average protein prop |   average rating |   average calories |  average n_ingredeients |  average prep time |
|:------------------|---------------:|---------:|-----------:|----------------:|------------:|
| False             |       0.119236 |  4.68317 |    425.378 |         9.0219  |     106.482 |
| True              |       0.408905 |  4.659   |    382.532 |         9.38522 |     108.736 |

---

## Assessment of Missingness

### NMAR Analysis

In our dataset, it is possible that the column `'review'` contains missing values that could be considered **Not Missing At Random (NMAR)**. Users who had an extremely negative or extremely positive experience with a recipe might be more likely to leave a review, while those who felt indifferent or uninterested might choose not to go through the hassle to leave a review on the recipe. This behavior is not directly captured in the data but could influence the missingness in the `'review'` column. 

To make the missingness in `'review'` potentially **Missing At Random (MAR)**, we could collect additional data about user behavior and experiences, such as
1. The reason for not submitting a review (e.g., "forgot," "not satisfied," "recipe incomplete").
2. Whether users opened or viewed the recipe multiple times but did not interact further.

### Missingness Dependency

Another column in the dataset that contains non-trivial missingness is `'rating'`, so I tested the dependency of the missingness of this column on other columns. Specifically, I will investigate whether the missiness in the `'rating'` column depends on the column `'protein prop'` or the column `'prep time'`.

> Dependency of the missingness of `'rating'` on `'protein prop'`

**Null Hypothesis:** The missingness of the `'rating'` column does not depend on the `'protein prop'` column.

**Alternate Hypothesis:** The missingness of the `'rating'` column does depend on the `'protein prop'` column.

**Test Statistic:** The absolute difference in the mean of `'protein prop'` between rows with and without missing ratings.

**Significance Level:** 0.05

**Permutation procedure:** Randomly shuffled the missingness of the `'rating'` column 1,000 times to simulate the null hypothesis where missingness is unrelated to the `'protein prop'` column.

<iframe
  src="assets/protein_prop.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value that the permutation test computed is **0.00**, we can **reject** the null hypothesis. This indicates a significant dependency between the missingness of the `'rating'` column and the `'protein prop'` column. It suggests that recipes with missing ratings have systematically different protein proportions compared to recipes with available ratings, which implies that missing ratings may be **Missing At Random (MAR)** with respect to protein proportions.

> Dependency of the missingness of `'rating'` on `'prep time'`

**Null Hypothesis:** The missingness of the `'rating'` column does not depend on the `'prep time'` column.

**Alternate Hypothesis:** The missingness of the `'rating'` column does depend on the `'prep time'` column.

**Test Statistic:** The absolute difference in the mean of `'prep time'` between rows with and without missing ratings.

**Significance Level:** 0.05

**Permutation procedure:** Randomly shuffled the missingness of the `'rating'` column 1,000 times to simulate the null hypothesis where missingness is unrelated to the `'prep time'` column.

<iframe
  src="assets/time_prop.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value that the permutation test computed is **0.126**, we **fail to reject** the null hypothesis. This suggests no strong evidence of a relationship between missingness of the `'rating'` column and the `'prep time'` columns. Missingness in this case might not be explainable by the '`prep time'` column, increasing the likelihood of the missingness being **Not Missing At Random (NMAR)**.

---
## Hypothesis Testing

**Hypotheses:**
- Null Hypothesis (H₀): High-protein recipes and low-protein recipes receive the same ratings (the distribution of ratings is the same for both groups).
- Alternative Hypothesis (H₁): High-protein recipes receive higher ratings than low-protein recipes (the mean of high-protein recipes is greater).

**Test Statistic:** The difference in means between high-protein and low-protein recipes.
- This choice is appropriate because it directly measures the specific aspect of the distributions we are interested in (the average rating difference).

**Significance Level:** 0.05

**Results:**
- Observed Difference in Means: -0.024
	- This indicates that on average, low-protein recipes received slightly higher ratings than high-protein recipes.
- P-value: 0.0
	- Indicating that it is highly unlikely to observe this difference or a more extreme one under the null hypothesis.

**Conclusion:**
- Since the p-value is less than 0.05, we reject the null hypothesis. This suggests that high-protein recipes receive significantly different ratings compared to low-protein recipes. However, the observed difference was negative, meaning high-protein recipes might receive lower ratings on average.

**Justification of Choices:**
- Difference in Means: The test statistic was chosen because it aligns with our question about the average rating difference between the two groups.
- Permutation Test: By shuffling the group labels and recalculating the test statistic, we avoid assumptions about the data's underlying distribution.
- Significance Level: Setting α at 0.05 is a common and reasonable threshold for determining statistical significance in exploratory analyses like this.

---
## Framing a Prediction Problem

**Prediction Problem and Type**
- The task is to predict the average rating of recipes based on their attributes. This is a regression problem since the response variable is continuous.

**Response Variable**
- The response variable is the `'avg rating'` column, representing the average rating of a recipe based on user feedback. This variable was chosen because understanding and predicting recipe ratings can provide insights into what factors contribute to highly rated recipes, which is valuable for both recipe creators and platforms hosting these recipes.

**Evaluation Metric**
- I chose the R² score (coefficient of determination) to evaluate the model's performance.

- While metrics like RMSE (Root Mean Squared Error) are useful for quantifying prediction error, R² is more interpretable in this context. It directly communicates the model's explanatory power, making it easier to assess its usefulness in predicting recipe ratings. It provides a clear measure of how much variance in the response variable is explained by the model. This is crucial in regression tasks, as it shows how well the model fits the data.

**Available Information**
- The information I would know at the time of prediction are the features in the `'recipes'` dataset. Those columns are features of the recipes on the website and it does not give any extra knowledge to what the rating for that recipe is at the time of prediction.

---
## Baseline Model
---
## Final Model
---
## Fairness Analysis
---
