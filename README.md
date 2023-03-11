# Classifying-Vulnerable-NY-Census-Tracts

## Business Question

In the wake of the COVID-19 (COVID) pandemic, the New York Department of Health (NYDH) is looking to improve the way they perform resource management. They would like to be able to identify which communities are vulnerable before an emergency so they have the right resources in place to address it. The proposed solution is to use data provided by the Center for Disease Control and Prevention (CDC) to build a machine learning model that can predict COVID levels for all New York census tracts. The NYDH can use this model and its features to continuously monitor the communities it serves to identify at risk populations.

## The Data

Data from two different CDC sources were used in building the models. The first dataset contained information relating to COVID prevlance in New York counties. Some examples include the number of confirmed cases, hospitalizations and deaths. These metrics were used to calculate overall COVID community levels which were classified as Low, Medium and High. These community levels were ultimately used as the target for the model. The second dataset included Census data for all tracts in New York. It is called the social vulnerability index (SVI) and includes socieconomic and demographic data about communities all across the state. Example dataset features include population estimates for various groups such as the estimated number of unemployed people, uninsured people or limited English speakers living in each census tract. The two datasets were ultimately mereged to allow for prediction of COVID community levels based on the SVI data. One assumption that was made in this process was that all census tracts in the same county have the same COVID community level. Since census tracts in the same county will be very close together and will usually share a healthcare system, this seemed like a reasonable assumption.    


## Data Cleaning

Prior to merging the datsets, some data cleaning needed to be applied. The rows of the COVID data were limited to those in New York counties and for the earliest update date (February 2022). Only the county identifier and community covid level were required for this model, so the rest of the columns were dropped. For the SVI data, most of the columns needed to be dropped as they were mostly recreations of another column. One example is that the dataset included population estimates for different groups, but also what percentage of the total population that group represents. The SVI data included a very detailed data dictionary which was used as a guide for which columns to drop. The data dictionary also included a guide for identifying missing data. For rows missing a small number of values, cells were flagged as NaNs for future imputation. Rows missing values in all columns were dropped from the dataset. Ultimately the datasets were merged together on the county data to create a final merged dataset. 

## Exploratory Data Analysis (EDA)

The first thing that was looked at was the distribution of the target variable:

![Target Distro](https://user-images.githubusercontent.com/66101132/224489058-bd63a68f-2f0c-4294-9a86-64a938748944.png)


There is a clear imbalance in the taget, particularly for the high COVID level class. Some sort of oversampling like Synthetic Minority Oversampling Technique (SMOTE) will need to be applied.

To help build the case for why identifying high COVID level areas is important, visualizations of the number of COVID cases by community level were created.

![COVID Cases By Class ](https://user-images.githubusercontent.com/66101132/224489065-44ff202d-11e8-4a3f-b86d-3e2c76820b21.png)

Although high COVID level census tracts make up a small percentage (~5%) of overall NY census tracts, they make up almost a third of COVID cases. Correctly identifying these areas will be extremely important to our stakeholders, even at the cost of more false positives. Along with accuracy, class 2 recall will be used as a metric for evaluating model performance.


Knowing what we know about how COVID is spread, it is also important to get a sense of how densly populated New York is:

![Distribution of Population by Tract Area](https://user-images.githubusercontent.com/66101132/224489075-2ca8df9c-2f11-4cf3-a7bb-878cdffa2939.png)

This scatterplot confirms that the majority of the New York population lives in small areas, which lends itself more to the spread of COVID. An early hypothesis is that both of these features will be highly influential. 


## Baseline and Model Iteration

For the baseline model, a simple logistic regression with ridge regularization was run. The model pipeline also included imputation of missing values using SimpleImputer and scaling all the numerical values with StandardScaler. Validation accuracy score was 82.4% and the class 2 recall was 11%. Although the model had a strong accuracy score, it was very inefficient at identifying the high COVID level class, which is the most important. Subsequent model iterations will include SMOTE to address the class imbalance problem. 

A summary of the model iterations with evaluations is listed below:




Many of the model versions struggled with overfitting and few were able to achieve a high recall score for the high COVID census tracts. Ultimately, the Untuned Logistic Regression with SMOTE was chosen as the final model for its balance between accuracy and recall. 

## Insights

Analysis of the key contributing factors in identifying COVID community levels was guided by the feature importance map generated from the models.


The top 5 most influential features are displayed above. Features like area of a census tract and the population living in crowded homes makes sense in the context of COVID and support the initial hypothesis. Other features like population living under the poverty level may provide more insight into the identifying characteristics of at risk communities.

Looking at the distribution of population in poverty for the model's predicted classes, high level COVID areas have a noticeably higher mean poverty level. 

![Insight 1 - Poverty](https://user-images.githubusercontent.com/66101132/224489111-bbcea9fb-2dc2-4800-939c-5ff4a64796d3.png)

Using statistical analyses like ANOVA and two tailed t-tests, it was confirmed that the high level COVID predictions have a meaningfully (95% Confidence level) higher level of population living in poverty.

Similar insights can be drawn for the number of people living in mobile homes. 

![Insight 2 - Mobile Homes](https://user-images.githubusercontent.com/66101132/224489115-c60c7754-e5a6-4224-bb7f-6eca18faca17.png)

## Recommendations 

Based on the findings of the model, the following recommendations would be shared with the NYHD for consideration:

* Regulary use this model to predict community vulnerability to health emergencies and allocate resources appropriately so they are in place before an outbreak  

* In the absence of complete data, use flags such as poverty levels and mobile home population as leading indictors of vulnerability

* Continuously update the model using the newest census data to stay on top of the risk present across the state

## Next Steps

For next steps, it would be interesting to expand the datasets to include healthcare access for different communities. It would also be interesting to explore other targets to see if this model generalizes to other emergencies like natural disasters.


