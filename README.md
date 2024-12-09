# Cooking Up Data: A Data Science Approach to Recipe Success
Conducted by Khoi Phan
## Introduction
The world is rapidly transitioning towards an increasingly "online" state, where digital technologies are transforming how we communicate, conduct business, access information, and interact with services across various aspects of life. One of many aspect being food; it is apparent that food is becoming a mainstream topic that society is constantly discussing about, either for fitness/health purposes or for hobbies like cooking and social media content creation.

In this project, I will be focusing on two datasets relating to food recipes posted since 2008 on [food.com](https://www.food.com/). Specifically, I am trying to answer the question: **"How do the nutritional characteristics of a recipe, specifically its protein content, impact its average user rating?"**. For recipe creators and social media food content creators, understanding how nutritional content affects ratings can help them optimize their recipes and content for higher ratings and interactions. For consumers, this insight might help them make healthier choices based on user feedbacks and ratings. Additionally, understanding the impact of protein content could help promote healthier eating habits.

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

## Data Cleaning and Exploratory Data Analysis
## Assessment of Missingness
## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis
