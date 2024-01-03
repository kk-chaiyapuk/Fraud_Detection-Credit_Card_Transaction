# Report - Fraud Analytics: Credit Card Transaction Fraud Detection

(There is no dataset provided. I only uploaded the code I used as well as the result. Please look at the attached PDF for a full report.)

## Executive summary
We utilized credit card transaction data from 2010 to create a credit card transaction fraud detection model that incorporated data from both credit card companies and merchants. Through out-of-time validation, we determined that the optimal threshold for fraud detection is a score cutoff of 4% of the total population. By adopting this threshold, we expect to achieve an estimated overall savings of $20,148,000, which considers the costs and benefits of fraud detection. Specifically, we anticipate a gain of $400 for every instance of fraud successfully detected, as well as a loss of $20 for each false positive.

![Credit Card Transaction Fraud Detection - Github](https://github.com/kk-chaiyapuk/Fraud_Detection_Credit_Card_Transaction/assets/82194433/d44e2157-c2d8-4a7a-93dd-f9e6854cadcd)

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

The variables were created withh transaction information as follows:
- **Day of the Week Risk Variable:** Fraud case percentage for each day of the week (with Statistical Smoothing to overall average percentage)
- **Benford’s Law Variables:** The ratio between amounts which first digit is 3-9 and amounts which first digit is 1-2, normalize by 1.096 according to Benford’s Law (plus Statistical Smoothing to 1).
- **Days Since Variables:** # days since a transaction with that entity has been seen. Entities list is attached below
- **Frequency Variables:** # records with the same entity over the last {0,1,3,7,14,30} days
- **Amount Variables:** Aggregation of "transaction amounts" with the same entity over the last {0,1,3,7,14,30} days, including average, max, total, standard deviation, actual/avg, actual/max, and actual/total
- **Amount Difference Variables:** Aggregation of "differences between recent transaction amount and previous amounts" with the same entity over the last {0,1,3,7,14,30} days, including average, min, max, total, and standard deviation
- **Relative Velocity Variables:** The ratio between {# records, total amounts} with the same entity over the past {0,1} days and the average of {# records, total amounts} with the same entity over the past {3,7,14,30} days
- **Relative Velocity per Day Since Variables:** The ratio between each Relative Velocity variables and Days Since variables with the same entity
- **Counts by Entities:** For each of the same entity over the past {0,1,3,7,14,30,60} days, # unique values of other entities

Entities list: ['Cardnum', 'Merchnum', 'Merch_description', 'Merch_num_des', 'Merch_num_state', 'Merch_num_zip', 'Merch_des_state', 'Merch_des_zip', 'Merch_state_zip', 'Merch_num_des_state', 'Merch_num_des_zip', 'Merch_num_state_zip', 'Merch_des_state_zip', 'Merch_all_info', 'card_Merchnum', 'card_Merch_description', 'card_Merch_state', 'card_Merch_zip', 'card_Merch_num_des', 'card_Merch_num_state', 'card_Merch_num_zip', 'card_Merch_des_state', 'card_Merch_des_zip', 'card_Merch_state_zip', 'card_Merch_num_des_state', 'card_Merch_num_des_zip', 'card_Merch_num_state_zip', 'card_Merch_des_state_zip', 'card_Merch_all_info']

### Section 4: Feature Selection
The first step is filtering variables independently based on their univariate model performance measure. Kolmogorov-Smirnov test for goodness of fit is used for this step. The result from the first step has 600 variables.

The second step is using a wrapper model with forward selection. LightGBM with n_estimators = 30 and num_leaves = 4 is used for this step. The result from the second step has 25 variables.
1. card_Merch_num_state_total_14
2. card_Merchnum_max_30
3. card_Merch_des_zip_total_1
4. Merch_description_total_0 
5. card_Merch_state_zip_total_14
6. card_Merch_des_state_max_30
7. card_Merch_des_zip_max_30
8. card_Merch_des_state_zip_max_30
9. card_Merch_num_des_zip_total_0
10. card_Merch_des_state_zip_total_30
11. card_Merch_num_state_zip_total_0
12. card_Merch_num_state_zip_total_30
13. card_Merch_des_zip_total_30
14. card_Merch_num_zip_total_0
15. card_Merch_des_zip_total_0
16. card_Merch_state_zip_total_0
17. card_Merch_num_state_total_30
18. Merch_state_zip_variability_avg_14
19. card_Merch_num_des_total_0
20. card_Merch_des_state_total_30
21. card_Merch_des_state_total_0
22. card_Merch_description_total_0
23. card_Merch_zip_total_0
24. card_Merch_num_des_zip_total_14
25. card_Merch_description_total_30

### Section 5: Preliminary Models Exploration
Variables from the feature selection process will be used to create several supervised models. Logistic regression models are used as a baseline for comparison. Non-linear models are expected to perform better than logistic regression models. Each model will be run 5 times and selected based on their average fraud detection rate at 3% population with the test dataset.

<img width="755" alt="image" src="https://github.com/kk-chaiyapuk/Fraud_Detection_Credit_Card_Transaction/assets/82194433/3a6d11f2-a374-4233-8f20-33ddfbe2ed4a">

### Section 6: Summary of Results
RandomForestClassifier (n_estimators = 20, min_impurity_decrease = 0.00001, min_samples_leaf = 30, min_samples_split = 60) with 10 variables has been chosen as the final model.

<img width="816" alt="image" src="https://github.com/kk-chaiyapuk/Fraud_Detection_Credit_Card_Transaction/assets/82194433/aed85f07-f6bf-4557-a801-5726f5abc247">

### Section 7: Recommended Fraud Score Cutoff
Based on out-of-time validation, we recommend ranking transactions based on the fraud likelihood and classifying the top 4% of the total population as fraudulent, which yields an expected overall savings of $20,148,000. This estimation is generated by considering the costs and benefits of detecting fraud, assuming a gain of $400 for every fraud caught and a loss of $20 for every false positive.

<img width="704" alt="image" src="https://github.com/kk-chaiyapuk/Fraud_Detection_Credit_Card_Transaction/assets/82194433/d97a508a-f336-4273-86f1-059585d3f458">

### Section 8: Summary
We utilized credit card transaction data from 2010 to create a credit card transaction fraud detection model that incorporated data from both credit card companies and merchants. Through out-of-time validation, we determined that the optimal threshold for fraud detection is a score cutoff of 4% of the total population. By adopting this threshold, we expect to achieve an estimated savings of $20,148,000, considering the costs and benefits of fraud detection. Specifically, we anticipate a gain of $400 for every instance of fraud successfully detected, as well as a loss of $20 for each false positive.
