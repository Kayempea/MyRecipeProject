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
	- Process: These datasets were merged using a left join on the recipe's ID.
	- Effect: This merge enabled me to combine recipe-specific features with user interaction data, such as ratings.

2. Handling Ratings of 0
	- Process: Replaced all ratings of 0 with NaN.
	- Effect: This prevented the use of erroneous ratings that could skew model performance and analysis.
3. Feature Transformation
	- Column: `'nutrition'`
		- Process: Split the `'nutrition'` column into individual columns: `'calories'`, `'total fat (PDV)'`, `'sugar (PDV)'`, `'sodium (PDV)'`, `'protein (PDV)'`, `'saturated fat (PDV)'`, and `'carbs (PDV)'`.
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
		- Effect: Enabled us to focus on high-protein recipes in alignment with my central question.
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

hello

<iframe
  src="assets/univariate.html"
  width="1000"
  height="600"
  frameborder="0"
></iframe>


---

## Assessment of Missingness
---
## Hypothesis Testing
---
## Framing a Prediction Problem
---
## Baseline Model
---
## Final Model
---
## Fairness Analysis
---
