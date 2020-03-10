# Predicting Hemoglobin a1c Levels in Diabetes Patients Using Frequency of Visits 



## Problem Statement

A non profit clinic wants to know whether or not how frequently a patient comes in for a visit is a determining factor in their overall A1c level. If a patient comes in more frequently, is there a higher likelihood that their A1c level is controlled? 

The clinic's senior data analyst provided me with anonymized data (HIPPA compliant) that included the following: 

### Data Dictionary 
| Feature | Type   | Dataset| Description  |
|------|------|------|------|
|   PatientId  | object|   A1c_Data_Requesta1c.csv | Original dataset patient id |
|------|------|------|------|
|   Age  | object|   A1c_Data_Requesta1c.csv  | Original dataset patient age |
|------|------|------|------|
|  Birthdate  | object|  A1c_Data_Requesta1c.csv  | Original dataset patient date of birth|
|------|------|------|------|
|   VisitDate  | object|   A1c_Data_Requesta1c.csv  | Original dataset visit dates of patient|
|------|------|------|------|
| SUMMARY  | object|   A1c_Data_Requesta1c.csv  |   Original dataset summary of patient visit|
|------|------|------|------|
|  OBSDATE  | object|   A1c_Data_Requesta1c.csv |  Original dataset date patient got bloodwork done|
|------|------|------|------|
|  Date of A1c | object|  A1c_Data_Requesta1c.csv  |  Original dataset patient a1c levels|
|------|------|------|------|
|  Insurance | object|   A1c_Data_Requesta1c.csv |  Original dataset patient insurance information |
|------|------|------|------|
| patient_id | int |   cleaned_data.csv |  Cleaned |
|------|------|------|------|
| age | float|  cleaned_data.csv  |  Cleaned|
|------|------|------|------|
| visit_date| datetime |   cleaned_data.csv  | Cleaned |
|------|------|------|------|
| summary | object|   cleaned_data.csv |  Cleaned |
|------|------|------|------|
| obs_date | datetime|   cleaned_data.csv |  Cleaned |
|------|------|------|------|
| a1c | float|   cleaned_data.csv |  Cleaned |
|------|------|------|------|
| a1c_level | float|   cleaned_data.csv |  Cleaned |
|------|------|------|------|
| patient_id| float|  new_df.csv  | Cleaned |
|------|------|------|------|
| age | float|   new_df.csv |  Cleaned |
|------|------|------|------|
| num_visits | float|   new_df.csv |  Cleaned |
|------|------|------|------|
| avg_time_between_visits | float|   new_df.csv |  Cleaned |
|------|------|------|------|
| avg_a1c | float|   new_df.csv |  Cleaned |
|------|------|------|------|
| avg_control_level| float|   new_df.csv |  Cleaned |
|------|------|------|------|
| num_obs| float|   new_df.csv |  Cleaned |

Our success can be validated if we are able to answer the following: 

1. Are patients more likely to have a lower and more controlled A1c if they have visited more frequently?
2. Can we predict based off of frequency of visits or last visit, what the patient's A1c is?
3. Might factors like age also have an impact on A1c levels? 
4. Since we have access to the summary of the patient visits, I also think it's worthwhile to use natural language processing in an attempt to predict control level.  

To get a better understanding of a1c levels, you can find an accurate depiction here: https://www.thediabetescouncil.com/wp-content/uploads/2016/09/IG-2-1-e1474921980344.jpg   

This clinic considered any a1c level above 8 as uncontrolled. 

## Executive Summary

I analyzed and cleaned the data, then built a model using the following process: 

### Data Cleaning
We have 273,485 rows of data and 8 columns. This data represents 1,099 patients. When cleaning the data, we need to consider the following:

#### Data normalization: a process where data in a database is organized in a way that the user can utilize it to conduct analysis and answer questions. This process entails eliminating redundancy and logically group data together. 

#### Data Clean
   - Changed column names so I could more easily work with data
   - Drop rows where A1c is null, since there were only 32 our of 273,485 rows
   - Change data types to represent datetime columns (observation date and visit date) and objects to floats. 
   - Remove or fix outliers: found a minimum too low for A1c. Changed .098 to 8.9 and dropped 1.7 
   - Create an additional column that marks each patients A1c as controlled as 1 or uncontrolled at 0 (dummy)
   - Each patient has multiple types of insurances listed, and we are unsure which is the most updated one or what many of them mean. For this reason, we have to eliminate this column for further analysis.
   
#### Data Split 
   - This data was not normalized, there are two dataframes grouped together in one in a way that doesn't make sense. One is responsible for a1c and when bloodwork was done, and the other holds information only on office visits. This also created many duplicates in the original dataset
    - I split the data into two dataframes and dropped the duplicates in each. One containing visit information, and the other containing bloodwork information 
    - This revealed that people come in a lot more frequently than they get their blood work taken. Originally having 61943 rows in each dataframe, I ended up with 3617 for bloodwork and 14197 for visit information. 
    
### Feature Engineering
   - I created a function to count how many unique visits and unique observation dates each patient had. That way, we could use the total number per each patient to see if this has an impact on A1c levels 
   - I calculated time between visits, and then from this the average time in between visits to be used as a feature for predicting whether or not the patient would be controlled or uncontrolled. 
   - To model using NLP, I combined and stemmed all summary text together and added it to each respective patient's row. 

### EDA:

   - Built a heatmap to see any strong correlations between a1c and other columns. 
   - Looked at average control level and average time between visits on a bar plot
   - Average control level and number of visits on a bar plot
   - Clustering to find patterns in data  
   - Looking at most frequently used words in both controlled and uncontrolled
   - Looking at most frequently used words in controlled
   - Looking at most frequently used words in uncontrolled
 
## Modeling

I decided to try both regression and classification modeling. Regression would be used to predict the patient's average a1c score. Classification would be used to predict whether or not the patient is controlled or uncontrolled.  

   - Linear regression was used to predict average a1c for each patient. The score here on the the training set: 0.07655737557026987 and the score on the test set: 0.08884825793752982
    - Our baseline score for classification was .60. Logistic regression had a score on the training set: 0.6754057428214731 and score on the test set: 0.6367041198501873. From this model, we can make a few inferences: 
        - As average time between visits increases by 1, someone is about 1.002 times as likely to be controlled.
        - As age increases by 1, someone is about 1.002 times as likely to be controlled.
        - As num visits increases by 1, someone is about 1.04 times as likely to be controlled.
        - As person has more bw obs done, they are less likely to be in the positive class/ controlled (.36 times as likely to be negative class) 
      -Decision Tree Classifier gave us a sensitivity of 0.3652 and specificity of 0.7237. 
      - NLP 
     
 
## Conclusions and Limitations

Our best score (.64) still isn't that much better than baseline (.60). Against initial thinking and on a very superficial level, we could draw the conclusion that the more time in between each patient, the more likely that patient will be controlled.  

The task at hand was more challenging given how the data was gathered, stored, and pulled. The inconsistency of visits and when blood work is drawn is also a limitation. 

## Future Work

I believe we can draw stronger conclusions if: 
- We could run a randomized experiment where patients had to come in every 1 month or 3 months and get their bloodwork done on the date of visit, to test the hypothesis that more frequent visits meant lower a1c levels. 
- The data was put together differently; Having one row per patient would have given us more clarity with how to approach modelling. 

I'd like to go back to make sure I've written my code efficiently. There are a few functions where I manipulate my data that could be more performative. For further investigation, we would need to do some hypothesis testing. I am going to continue to feature engineer to see if we've missed any important features that, when put together, impact a1c. I'd also like to experiment with cleaning the data differently - perhaps using the average levels for a1c and average time between visits wasn't the best approach. 
