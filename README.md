![For Sale House](assets/forsale.png)

# Ames Iowa Property Sale Price  Prediction Model

## Problem Statement

The purpose of this project is to demonstrate that the final selling price of a house may be accurately modeled via simple Ordinary Least Squares regression. In doing so, one is afforded insight to the impact of individual housing features on the predicted sale price, thereby informing us about the potential “best” and “worst” types of projects to undertake in an attempt to add value to the property.

## Executive Summary

Our exploration of sale price prediction models led us to create 3 separate models: one an OLS, and the other two, instances of RidgeCV and LASSO-CV regularizations of the original linear model. Before one can begin modeling however, it is necessary to undertake robust data cleaning measures from simply adjusting names of individual features to “play nice” with the Pandas library, to various missing value imputation strategies. From here, post-cleaning exploratory analysis paved the way for custom feature engineering via interaction terms and column/feature reduction. After actually taking this data to model, we of course engage in discussion of the models’ validities through an exploration of feature coefficients and LINEM assumption testing.

## Introduction

Predicting the final sale price of real estate is an effort often undertaken for the sake of investment, timing the market, or otherwise attempting to measure and extract the value of particular properties. While one may correctly intuit the significant impact core features such as square footage, lot size, and the total numbers of bedrooms and bathrooms, the details often slip through the cracks. Thanks to the incredibly robust Ames Iowa Housing Dataset, our model is able to incorporate these smaller features to improve our model’s accuracy over the simpler case. In doing so, we gain crucial information about the types of features that help and hurt the final sale price of these properties, enabling us to make inferences about the best types of projects for increasing the value of a property presale.

## Data

For this model, we made use of 2 datasets located in the `datasets` folder of the project directory, `train.csv` and `test.csv`.  Aptly named, the training data is used to fit our model before making predictions on the final sale prices of properties in the test data set. The predictions for the testing set were then submitted to Kaggle in a friendly data science competition between me and my cohort mates.

### Data Dictionary

It is unnecessary to undertake a detailed explanation of each feature present in the original dataset, as Dean de Cock has done a fantastic job of providing such information [here.](https://jse.amstat.org/v19n3/decock/DataDocumentation.txt). However, we do engage in significant feature engineering during the data preparation phase, and thus these new features should be addressed in an addendum to the above linked data dictionary.

| Feature Name | Parent Feature(s) | Data Type | Description |
| --- | --- | --- | --- |
| age | year_sold, year_remod_or_add | int | The number of years since the property was constructed OR the number of years since the house was remodeled or renovated, whichever is lesser. |
| bathrooms | bsmt_full_bath, bsmt_half_bath, full_bath, half_bath | int | Total number of bathrooms on site |
| pool (dummy) | pool_area | int (0/1) | Whether or not the property has a pool |
| regular_lot (dummy) | lot_shape | int (0/1) | Whether or not the lot shape is labelled as ‘Regular’ |
| level_contour (dummy) | land_contour | int (0/1) | Whether or not the property has a level contour |
| pos_feat_nb (dummy) | condition_1, condition_2 | int (0/1) | Whether or not the property is nearby an Artery or feeder street, has a positive feature nearby, or is in the area of a positive feature. For discussion on what counts as a positive feature, please see Dean de Cock’s data dictionary linked above. |
| neigh_score | neighborhood | int (1,2,3) ordinal | Corollary for neighborhood “desirability”. See investigation in the Cleaning and EDA Notebook for additional details. |
| location | overall_qual, year_built, gr_liv_area | float | Precursor for neigh_score. A standardized measure of summed z-scores for the parent features. Mainly used to identify and categorize neighborhoods based on desirability.  |
| total_sqft | total_bsmt_sqft, gr_liv_area, garage_area | float | Total sqft for property.  |

___

## Data Cleaning, Feature Selection, and Data Engineering

See Data_cleaning_and_EDA.ipynb.

___

## Model Evaluation

| Model | R2 (train, test) | RMSE (train, test) |
| --- | --- | --- |
| OLS | (0.94, 0.90) | (14167, 19082) |
| Ridge CV | (0.94, 0.90) | (14988, 18849) |
| LASSO CV | (0.93, 0.90) | (15743, 18595) |

All models had an R2 score above 90%. LASSO CV performed best according to the RMSE loss function, but OLS actually ended up generating the strongest scores for the Kaggle competition. We see here that our models all have fairly low variance considering their object (house prices). The introduction of bias via regularization did seem to help reduce the variance, but only marginally so.

___

## Feature Coefficient Exploration and Model Validation

See the section at the end of 03_Production_model_and_Eval.ipynb for more information.

![OLSFeatBarplot](../project_2/assets/olsfeatbarplot.png)

The above graph indicates the 9 most impactful features in the set based on their absolute mean SHAP value calculated using all predictions. As we can see, overall quality and conditions in the middling range of 5-7 appear to be extremely influential across all predictions. 2nd floor area also has a significant positive impact on the prediction value, likely due to the fact that it simultaneously indicates additional space and the presence of a 2nd story on the property. Likewise, kitchen quality, the total above grade area, and the year built are quite important to our model's predictions.

> More succinctly:
> For each feature ***y*** on our bar chart, we can expect the value of an individual prediction to increase by the magnitude of the corresponding bar displayed, all else held equal.
>

![OLSBeeswarmPlot](../project_2/assets/olsbeeswarm.png)

Alternative view to the bar plot above.

___

## Conclusions and Recommendations

To make accurate recommendations about which features add the most value to a property, we have to look to the SHAP scores of our Ridge and LASSO models, as these indicate features that are able to be changed rather than summary quantities like overall condition and overall quality. From those plots (seen in the Model Evaluation section of the Production Model notebook), one derives the following top 5 feature recommendations:

| Feature | Mean SHAP Value Added |
| --- | --- |
| fireplaces | 3568.73 |
| brick exterior | 3241.27 |
| bathrooms | 3212.06 |
| garage cars | 2227.77 |
| screen porch | 2111.96 |
