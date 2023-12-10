# Report - Fraud Detection Algorithm - Credit Card Transaction

(There is no dataset provided. I only uploaded the code I used as well as the result. Please look at the attached PDF for a full report.)

## Executive summary
We utilized credit card transaction data from 2010 to create a credit card transaction fraud detection model that incorporated data from both credit card companies and merchants. Through out-of-time validation, we determined that the optimal threshold for fraud detection is a score cutoff of 4% of the total population. By adopting this threshold, we expect to achieve an estimated overall savings of $20,148,000, which considers the costs and benefits of fraud detection. Specifically, we anticipate a gain of $400 for every instance of fraud successfully detected, as well as a loss of $20 for each false positive.

## Overview
The report has been included in this repository with 7 sections as follow:

### Section 1: Description of Data
This is transaction data containing credit card transactions in 2010. There are 96,753 transactions with 10 fields as follows:
- Date - Transaction date
- Amount - Transaction amount
- Recnum - A record number. Ordinal unique positive integer for each record.
- Cardnum - Credit card number
- Merchnum - Merchants' ID
- Merch description - Merchants' name
- Merch state - Merchants' two-letter U.S. state and territory abbreviations
- Merch zip - Merchants' 5 digits zip code
- Transtype - Transaction type includes purchase, authorization, etc.
- <b>Fraud - (This is our target variable)</b> A binary field for fraud applications

### Section 2: Data Cleaning
The data had been filtered and kept only Transtype == 'P' before missing value imputation.
There are three variables with missing values which are Merchnum, Merch state, and Merch zip.

### Section 3: Feature Engineering
The 3,792 variables have been created with transaction information. Credit card transaction fraud is the case when a credit card is used by someone other than the owner. The supervised algorithm will look for characteristics of fraud cases based on variables that are created from account and merchant information. The algorithm will investigate if any card has been used with an abnormal number of times/amounts.

### Section 4: Feature Selection
The first step is filtering variables independently based on their univariate model performance measure. Kolmogorov-Smirnov test for goodness of fit is used for this step. The result from the first step has 600 variables.

The second step is using a wrapper model with forward selection. LightGBM with n_estimators = 30 and num_leaves = 4 is used for this step. The result from the second step has 25 variables.

### Section 5: Preliminary Models Exploration
Variables from the feature selection process will be used to create several supervised models. Logistic regression models are used as a baseline for comparison. Non-linear models are expected to perform better than logistic regression models. Each model will be run 5 times and will be selected based on their average fraud detection rate at 3% population with the test dataset.

### Section 6: Summary of Results
RandomForestClassifier (n_estimators = 20, min_impurity_decrease = 0.00001, min_samples_leaf = 30, min_samples_split = 60) with 10 variables has been chosen as the final model.

### Section 7: Recommended Fraud Score Cutoff
Based on out-of-time validation, we recommend classifying the 4% riskest of the total population as fraudulent, which yields an expected overall savings of $20,148,000. This estimation is generated by considering the costs and benefits of detecting fraud, assuming a gain of $400 for every fraud caught and a loss of $20 for every false positive.

### Section 8: Summary
We utilized credit card transaction data from 2010 to create a credit card transaction fraud detection model that incorporated data from both credit card companies and merchants. Through out-of-time validation, we determined that the optimal threshold for fraud detection is a score cutoff of 4% of the total population. By adopting this threshold, we expect to achieve an estimated overall savings of $20,148,000, which considers the costs and benefits of fraud detection. Specifically, we anticipate a gain of $400 for every instance of fraud successfully detected, as well as a loss of $20 for each false positive.
