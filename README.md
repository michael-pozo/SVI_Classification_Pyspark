# Classifying-Vulnerable-NY-Census-Tracts

## Business Question

In the wake of the COVID-19 (COVID) pandemic, the New York Department of Health (NYDH) is looking to improve the way they perform resource management. They would like to be able to identify which communities are vulnerable before an emergency so they have the right resources in place to address it. The proposed solution is to use data provided by the Center for Disease Control and Prevention (CDC) to build a machine learning model that can predict COVID levels for all New York census tracts. The NYDH can use this model and its features to continuously monitor the communities it serves to identify at risk populations.

## The Data

Data from two different CDC sources were used in building the models. The first dataset contained information relating to COVID prevlance in New York counties. Some examples include the number of confirmed cases, hospitalizations and deaths. These metrics were used to calculate overall COVID community levels which were classified as Low, Medium and High. These community levels will ultimately be used as the target for our model. The second dataset included Census data for all tracts in New York. Dataset features include population estimates for various different groups such as the estimated number of unemployed, uninsured or limited English speakers live in each census tract. (come back)  


## Data Cleaning

Prior to merging the datsets, some data cleaning needed to be applied. The rows of the COVID data were limited to those in New York counties and for the earliest update date (February 2022). Only the county identifier and community covid level were required for this model, so the rest of the columns were dropped. For the SVI data, most of the columns needed to be dropped as they were mostly recreations of another column. One example is that the dataset include both population estimates for different groups, but also what percentage of the total population that group represents. The SVI data included a very detailed data dictionary which was used as a guide for which columns to drop. The data dictionary also included a guide for identifying missing data. For rows missing a small number of values, they were flagged as NaNs for future imputation. Rows missing values in all columns were dropped from the dataset. Ultimately the datasets were merged together on the county data to create a final merged dataset. 

## Exploratory Data Analysis (EDA)

The first thing that was looked at was the distribution of the target variable:


There is a clear imbalance in the taget, particularly for the high risk class. Some sort of oversampling like Synthetic Minority Oversampling Technique (SMOTE) will need to be applied.

To help build the case for why identifying high COVID level areas is important, visualizations of the number of COVID cases by community level were created


Although high COVID level census tracts make up a small percentage (~5%) of overall NY census tracts, they make up almost a third of COVID cases. Correctly identifying these areas will be extremely important to our stakeholders, even at the cost of more false positives. Along with accuracy, class 2 recall will be used as a metric for evaluating model performance.


Knowing what we know about how COVID is spread, it is also important to get a sense of how densly populated New York is:


This scatterplot confirms that the majority of the New York population lives in small areas, which lends itself more to the spread of COVID. An early hypothesis is that both of these features will be highly influential 


## Baseline and Model Iteration

For the baseline model, a simple logistic regression with ridge regularization was run. The model pipeline also included imputing missing values using SimpleImputer and scaling all the numerical values with StandardScaler. Validation accuracy score was 82.4% and the class 2 recall was 11%. Although the model had a strong accuracy score, it was very inefficient at identifying the high COVID class which is the most important. Subsequent model iterations will include SMOTE to address the class imbalance problem. 

A summary of the model iterations with evaluations is listed below:



