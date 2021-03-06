---
layout: post
title: "I'd sleep there"
subtitle: "Take a trip back to 2016 and visit Seattle... or at least the AirBnB reviews"
tags: [Data Science, Data Science Student, Data Cleaning, Data Wrangling, Data Analysis, Machine Learning, AirBnB, Seattle]
---
<p align="center">
  <img width="400" height="250" src="https://i.imgur.com/aMYAXoi.png?1" class="align-center">
</p>


### **Introduction**
As I stumbled upon this dataset that was created in January 2016 and graciously posted on Kaggle, I felt it deserved further analysis. After looking over the data held within it's 92 columns and 3800+ observations I wanted to see how well I could recommend rental properties based on the various reviews. The process included a lot of cleaning, creating and converting feature categories, as well as fine-tuning and running various predictive models. Let's take a look at my journey as I work to recommend AirBnB rental properties. 

### **My Process**
While first analyzing the data I found many columns not to be of much use, such as URL's and categories with only one unique identifier. For instance in the column 'state', Washington is included in every observation. I decided that I wanted to use review data to create a target for recommending properties. There were several review categories containing information on various reviews. Luckily for me the original creator of this dataset who web-scraped the data from Air BnB's website made a feature based on several review categories such as the cleanliness of the rental and host communication. These reviews were all based on a 1 - 10 scale with 10 being the best. The feature I was most interested in contained the average of all reviews in percentage format titled 'review_scores_rating'.

After doing an initial column drop of many categories of URLs and thumbnails, I also dropped columns with one unique identifier, as well as columns of little importance and high variance. It was then time for me to create a target based on 'review_scores_rating' and the percentages contained in that column. I created a new feature titled 'highly_recommend' and set it to anything equal to or higher than 95%, which values represented properties I would highly recommend. All other values, I would not highly recommend. In this process I created a binary classification problem that answers the question "do I highly recommend this rental property?" and the answer is simply yes or no, or in this case true or false. In order to avoid leakage I converted the boolean values to 0s and 1s, with 0 representing no or false, and 1 being yes I would highly recommend this AirBnB property. 

I then dropped and converted categories into classes until I had a dataset that I could do some predictive modeling with, hoping that all of the feature engineering would avoid leakage or interaction between the features and target. To identify the property by location I kept 'latitude' and 'longitude' but engineered a low cardinality feature that clustered the coordinates into 5 classes. As another measure of identification, although it contained variance and high cardinality, I kept "property_id' because if I am going to recommend properties, people are going to want to know where they are! 

>If you look at the map below you will see that there are no major outliers and are all indeed in the city limits of Seattle.
### **Interactive Map: all properties in the data**
{% include figure.html %}

## **Splitting the data and getting a target baseline**
Down to only 20 columns I was ready to split the data into training, validation and test sets. I used a random split where the test data consisted of 20% of the overall data. I then set the X features and y vector for each of these sets of data. Now it was time to create my baseline. I did this by calculating the value counts of class 1 and 2 in the 'highly_recommend' column of the training data. This returned a baseline of roughly 53% of the rentals that I would highly recommend. 

<p align="center">
  <img width="300" height="300" src="https://i.imgur.com/NZuv8Bf.png">
</p>

## **Logistic Regression**
The initial linear model I chose was logistic regression with ordinal encoding, which gave me an accuracy score of 65%, beating the baseline by 12 percentage points. I was surprised by the jump in accuracy so I wanted to take a look at the coefficients as they are great estimates of the unknown population parameters, therefore the model depends on coefficients for accurate predictions.   

### **Plotting the coefficients of the logistic regression model**
<p align="center">
  <img width="350" height="400" src="https://i.imgur.com/ri0SmJg.png">
</p>

## **Decision Tree**
The first classification metric I decided to use was a simple decision tree classifier with ordinal encoding. I fit the model with a low score of 59%. This score is only 6 percentage points over baseline which I found surprising. In hindsight I could've fine tuned the hyperparameters for a slightly higher score. Another reason the decision tree was potentially "thrown off" was due to the identifying features with high cardinality such as 'longitude', 'latitude' and 'property_id'. You can see how strongly they impacted the model by looking at the visual below. 

### **Decision Tree Model**
<p align="center">
  <img width="800" height="500" src="https://i.imgur.com/FHmH3Te.png">
</p>

## **Random Forest**
The next classification metric I chose was a random forest classifier with ordinal encoding. I was really hoping for a better score using a more complex model, which gave me a validation accuracy score of 66%. This score beats the baseline and the decision tree score but only out performs the linear model by 1 percentage point. So I dug a little deeper. 

### **Confusion Matrix for the Random Forest model**
<p align="center">
  <img width="500" height="400" src="https://i.imgur.com/sGnrnmG.png">
</p>

### **Feature Importances of the Random Forest model**
<p align="center">
  <img width="600" height="700" src="https://i.imgur.com/XjQa7LV.png">
</p>

### **Permutation Importance with eli 5**
<p align="center">
  <img width="300" height="425" src="https://i.imgur.com/pFMwTV5.png">
</p>

## **XG Booster**
I then used XG Booster with ordinal encoding. That model gave me an accuracy score of 64%. Not very impressive but I could always fine tune hyperparameters for a better score. 

## **ROC Curve and ROC/AUC Scores**
Now I am going to show a visual of the ROC curve for 'highly_recommend' from every model I used to fit our training data to get validation accuracy.

<p align="center">
  <img width="500" height="400" src="https://i.imgur.com/NMH11fE.png">
</p>

When looking at the visual you can see Random Forest fit the best, with Logistic Regression and XG Boost coming very close. Logistic Regression is close to the boundary line that splits True Positive and False Positive.  Here are the following scores for each model:

- Logistic Regression ROC/AUC Score: 0.70
- Decision Tree ROC/AUC Score: 0.61
- Random Forest ROC/AUC Score: 0.71
- XG Booster ROC/AUC Score: 0.70

## **Conclusion**
Although all of the models beat the baseline the Logistic Regression, Random Forest and XG Booster were all very close. The Decision Tree model did not fare as well as the others but still beat the baseline. There are many ways to improve scores here but one would have to reevaluate the pre-processing methods and classification metrics used. Perhaps there was too much predictive modeling, maybe leaving in the location features with high cardinality caused leakage. This was great fun putting together, but how relevant are these models in 2020? Probably not that much, especially with Covid changing the way AirBnB property owners can conduct business. However, in 2016 this project would have been a great guide for recommending rentals to out of town visitors. 

<p align="center">
  <img width="200" height="200" src="https://i.imgur.com/n4NVO3e.png" class="align-center">
</p>

## **About the Data**
The original data can be found [here](https://www.kaggle.com/airbnb/seattle/data) in a zipped folder. The original dataset for this project is listings.csv.

## **See the code**
The notebook for this project can be found [here](https://colab.research.google.com/drive/1QaOmt9Uw1fEkdvTAWO-gSPDR9fSQ4Fo3?usp=sharing) 

## **About the Author**
Jay Adamo is a current Data Science student at Lambda School on track for full endorsement January 2021. Jay is a former resident of Seattle, Washington and an avid user of AirBnB. Jay Adamo is in no other way affiliated with AirBnB and this project was undertaken as a showcase of his Data Science skillset.
