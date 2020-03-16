# Predicting Hemoglobin A1c Levels in Diabetes Patients Using Frequency of Visits 



## Problem Statement

A non profit clinic wants to know whether or not frequency of patient visits has an impact on their overall A1c level. If a patient's A1c level is at or above 8, they are categorized as being uncontrolled. This is an unhealthy state for a diabetic individual to be in, and they are advised to take action to decrease it. If a patient comes in more frequently, is there a higher likelihood that their A1c level is controlled? Although correlation does not imply causation, we'd like to explore potential patterns in the data.

Our success can be validated if we are able to answer and/or do the following: 

1. Are patients more likely to have a lower and controlled A1c if they visit more frequently?
2. Can we predict based off of frequency of visits or time in between visits, what the patient's A1c is?
3. Might factors like age also have an impact on A1c levels? 
4. Since we have access to the summary of the patient visits, I also think it's worthwhile to use natural language processing in an attempt to predict control level.  

To get a better understanding of a1c levels, you can find an accurate depiction here: https://www.thediabetescouncil.com/wp-content/uploads/2016/09/IG-2-1-e1474921980344.jpg. This clinic considered any a1c level above 8 as uncontrolled. 

## Table of Contents
1. [Data Cleaning](https://github.com/dmiz6393/diabetes_a1c/blob/master/code/1_Data_Clean.ipynb)
3. [Data Cleaning and Spliting Data](https://github.com/dmiz6393/diabetes_a1c/blob/master/code/2_Data_Clean_and_Split.ipynb)
3. [Feature Engineering](https://github.com/dmiz6393/diabetes_a1c/blob/master/code/3_Feature_Engineering.ipynb)
3. [Feature Engineering](https://github.com/dmiz6393/diabetes_a1c/blob/master/code/4_Feature_Engineering.ipynb)
3. [Feature Engineering](https://github.com/dmiz6393/diabetes_a1c/blob/master/code/5_Feature_Engineering.ipynb)
4. [Exploratory Data Analysis](https://github.com/dmiz6393/diabetes_a1c/blob/master/code/6_Exploratory_Data_Analysis%20.ipynb)
5. [Modeling](https://github.com/dmiz6393/diabetes_a1c/blob/master/code/7_Modeling.ipynb)

The clinic's senior data analyst provided me with anonymized data (HIPPA compliant) that included the following: 

### Data Dictionary 
| Feature | Type   | Dataset| Description  |
|------|------|------|------|
|   PatientId  | object|   A1c_Data_Requesta1c.csv | Original dataset patient id |
|   Age  | object|   A1c_Data_Requesta1c.csv  | Original dataset patient age 
|  Birthdate  | object|  A1c_Data_Requesta1c.csv  | Original dataset: patient date of birth|
|   VisitDate  | object|   A1c_Data_Requesta1c.csv  | Original dataset: visit dates of patient|
| SUMMARY  | object|   A1c_Data_Requesta1c.csv  |   Original dataset: summary of patient visit|
|  OBSDATE  | object|   A1c_Data_Requesta1c.csv |  Original dataset: date patient got bloodwork done|
|  Date of A1c | object|  A1c_Data_Requesta1c.csv  |  Original dataset: patient a1c levels|
|  Insurance | object|   A1c_Data_Requesta1c.csv |  Original dataset patient insurance information |
| patient_id | int |   cleaned_data.csv |  Part of the cleaned version of data :patient's id |
| age | float|  cleaned_data.csv  |   Part of the cleaned version of data: patient's age|
| visit_date| datetime |   cleaned_data.csv  | Part of the cleaned version of data: data of patient's visit|
| summary | object|   cleaned_data.csv |  Part of the cleaned version of data: summary of patient's visit |
| obs_date | datetime|   cleaned_data.csv |   Part of the cleaned version of data: date that a patient got their blood drawn |
| a1c | float|   cleaned_data.csv |  Part of the cleaned version of data: a1c of patient on date of observation |
| a1c_level | float|   cleaned_data.csv |  Part of the cleaned version of data: a1c level of patient on date of observation |
| patient_id| float|  new_df.csv  | Data used for modelling |
| age | float|   new_df.csv |  Data used for modelling |
| num_visits | float|   new_df.csv |  Data used for modelling|
| avg_time_between_visits | float|   new_df.csv |  Data used for modelling |
| avg_a1c | float|   new_df.csv |  Data used for modelling |
| avg_control_level| float|   new_df.csv |  Data used for modelling|
| num_obs| float|   new_df.csv |  Data used for modelling |

### Data Files: What each file represents
- [A1c_Data_Requesta1c.csv - original dataset](https://github.com/dmiz6393/diabetes_a1c/blob/master/data/A1c_Data_Requesta1c.csv)
- [cleaned_data.csv - cleaned dataset](https://github.com/dmiz6393/diabetes_a1c/blob/master/data/cleaned_data.csv) 
- [date_a1c_df.csv- dataset with  observation date and a1c levels](https://github.com/dmiz6393/diabetes_a1c/blob/master/data/date_a1c_df)
- [new_df.csv - dataset used for modeling, each row represents a different patient](https://github.com/dmiz6393/diabetes_a1c/blob/master/data/new_df)
- [sorted_sum_data.csv - dataset that includes patient visit information,average time between visits, and average a1c for each patient](https://github.com/dmiz6393/diabetes_a1c/blob/master/data/sorted_sum_data.csv)
- [summary_and_visit_df.csv - dataset that includes patient visit information](https://github.com/dmiz6393/diabetes_a1c/blob/master/data/summary_and_visit_df)
- [sorted_summary_with_avg_visits.csv - dataset that includes patient visit information and average time between visits for each patient](https://github.com/dmiz6393/diabetes_a1c/blob/master/data/summary_and_visit_df)



## Executive Summary

I analyzed and cleaned the data, then modelled using the following process: 

### Data Cleaning and Normalization 
We have 273,485 rows of data and 8 columns. This data represents 1,099 patients. This is the process I took to normalize and clean the data: 

#### Data Clean
   - Changed column names 
   - Dropped rows where A1c is null, since there were only 32 our of 273,485 rows
   - Changed data types to represent datetime columns (observation date and visit date) and objects to floats. 
   - Removed or fix outliers: found a minimum too low for A1c. Changed .089 to 8.9 and dropped 1.7 a1c levels.  
   - Created an additional column that marks each patients A1c as controlled as our positive class 1 or uncontrolled as our negative class 0 
   - Each patient has multiple types of insurances listed, and we are unsure which is the most updated one or what many of them mean. For this reason, we eliminated this column for further analysis.
   
#### Data Split 
   - This data was not normalized, there are two dataframes grouped together in a way that makes it difficult to analyze. One is responsible for a1c and when bloodwork was done, and the other holds information only on office visits. This also created many duplicates in the original dataset
    - I split the data into two dataframes and dropped the duplicates in each. One containing visit information, and the other containing bloodwork information 
    - In doing so, I discovered that people come in a lot more frequently than they get their blood work taken. Originally having 61943 rows in each dataframe, I ended up with 3617 for bloodwork and 14197 for visit information. 
    
### Feature Engineering
Since there were a few things done to manipulate the data, there are 3 notebooks dedicated to feature engineering. This was done so that when I or anyone else come back to the project, certain functions that take some time to run to always have to be rerun. The updated data is saved in a csv for this reason.  

   - I created a function to count how many unique visits and unique observation dates each patient had. That way, we could use the total number per each patient to see if this has an impact on A1c levels 
   - I built a function that calculated time between visits, and then from this the average time in between visits to be used as a feature for predicting whether or not the patient would be controlled or uncontrolled. 
   - To model using NLP, I combined and stemmed all summary text together and added it to each respective patient's row. 
   - I built a function to add average A1c of each patient as an additional column, and from this added a column to represent a patient's average control level. 

### EDA:

   - Built a heatmap to see any strong correlations between a1c and other columns. Although heatmaps shouldn't usually be used with data concerning health, I was curious to see if there would be any type of correlation with a1c. The chart doesn't show that there is.
   - Looked at average control level and average time between visits on a bar plot. There didn't seem to be a significant difference between number of visits and control level.
   - Plotted average control level(controlled or uncontrolled) and number of visits on a bar plot. Most of our data points are clustered to the bottom left hand side with a few outliers.
   - Clustering by average time between visits: we see there is a pattern in how much time patients take to come back for a visit, but this doesn't show a difference in their a1c levels. 
   - Plotting histogtams to see if features in our data follow a normal distribution: it doesn't. 
   - Looked at most frequently used words in both controlled and uncontrolled
   - Looked at most frequently used words in controlled
   - Looked at most frequently used words in uncontrolled
       - Words like hypertension, diabetes, lab, check and refill appear to be in both controlled and uncontrolled groups. I didn't see any major differences in language used for each type of patient.
 
## Modeling

I decided to try both regression and classification modeling. Regression would be used to predict the patient's average A1c score. Classification would be used to predict whether or not the patient on average was controlled or uncontrolled. 

### Linear Regression
Linear regression was used to predict average a1c for each patient. It is also worth noting that with this type of data, it is unlikely that all MLR assumptions are being met. 

How well does the model predict average a1c if we just use average time between visits and number of visits for our features? 
We get a score on the training set of 0.006 and 0.0116 on the testing set. If we add in age and number of observations,  we still get a low score on the training set of 0.076 and a score of 0.088 on the testing set. The score doesn't change even when we round our a1c levels.
   
### Logistic Regression
Next I wanted to see if we could better predict average control level (whether or not on average, a patient is controlled or uncontrolled) using logistic regression. Our baseline score for classification was .60. Logistic regression had a score on the training set of 0.675 and score on the test set of 0.636. 

From this model, we can make a few (relatively unhelpful) inferences: 
        - As average time between visits increases by 1 day, someone is about 1.002 times as likely to be controlled.
        - As age increases by 1, someone is about 1.002 times as likely to be controlled.
        - As num visits increases by 1, someone is about 1.04 times as likely to be controlled.
        - As person has more observations done, they are less likely to be in the positive class and controlled (.36 times as likely to be negative class) 
   
### Decision Tree Classifier 

The Decision Tree Classifier gave us a sensitivity of 0.3652 and specificity of 0.7237. Using gridsearch, our best score on the testing set was .57. In a healthcare setting, usually you want to optimize for sensitivity. Here, our specificity score (optimizing for true negatives) is much higher than our sensitivity score (optimizing for true positives). We would rather tell a patient who is controlled that they are not as opposed to telling a patient they are controlled when they are not. 

### Random Forest Classifier 

In a healthcare setting, usually you want to optimize for sensitivity. Here, our specificity score (optimizing for true negatives) is much higher than our sensitivity score (optimizing for true positives). We would rather tell a patient who is controlled that they are not as opposed to telling a patient they are controlled when they are not. With Random Forest Classifier, we will attempt to optimize for sensitivity using Kevin Arvai's post and code on how to optimize for sensitivity: https://towardsdatascience.com/fine-tuning-a-classifier-in-scikit-learn-66e048c21e65. Kevin talks about two ways to optimize for sensitivity, we use the first he mentions in the post: GridSearchCV to tune your model by searching for the best hyperparameters and keeping the classifier with the highest recall score. Using this, we still end up with an accuracy score close to baseline and a recall score of .209. 

### Natural Language Processing on summary text for patient visits

Here, I used both CountVectorizer and TfidVectorizer and MultinomialNB to see which transformer would give the best result, including stop words and no stop words. Our NLP best score was .635. We used the "clean_text" feature to predict whether or not the patient was 0(uncontrolled) or 1 (controlled). The clean_text feature was the stemmed version of all the summary text summed together from the patient's every visit. We train/test/split and created a pipeline and gridsearch using both CountVectorizer and TfidVectorizer with MultinomialNB to see which transformer would give the best result.

Modelling using NLP still doesn't perform that much better than our baseline score
     
 
## Conclusions and Limitations

When attempting to predict average A1c of patient's based soley off of average time between visits and number of visits, our model does not perform well. Even when we add in age and number of observtions for each patient, there is little improvement. Our best accuracy score for prediting if a patient was controlled or uncontrolled on average is .64, which still isn't that much better than our baseline score of .60. It seems that from this dataset, frequency of visits and average time between visits for each patient is not a good indicator of average a1c or control levels. There was perhaps some indication that more time in between visits was correlated with lower average a1c levels, but we'd need to investigate this further in a controlled experiment. 

It is difficult to make any solid conclusions from this analysis given the limitations of the project and lack of knowledge on the subject matter. The task at hand was more challenging given how the data was gathered, stored, and pulled. The inconsistency of visits and when blood work drawn also acted as a limitation. Having to manipulate the data (taking the average number of days between visits and average a1c levels to model and make predictions) also doesn't seem like the optimal approach given how unreliable averages can be at times. 

Finally, correlation does not mean causation - perhaps the patients who on average are controlled, come in for fewer visits since they don't feel the need to see a doctor. 


## Future Work

### Running an experiment to test hypothesis 
I believe we can draw stronger conclusions if we could run a randomized experiment where patients had to come in every 1 month or 3 months for a visit and must get their bloodwork done on the date of visit. This would allow us to more accurately test the hypothesis that more frequent visits meant lower a1c levels. I'd also like to conduct more research to understand all factors that contribue to a patient's A1c level.  

### Data Normalization 
The data was put together differently; Having one row per patient would have given us more clarity with how to approach modelling. Additionally, I'd like to look at each individual patient's data as a whole. Was the patient's a1c lower when they waited less time for their next visit? Perhaps having data on fewer patients but in an organized manner would also help with this type of analysis, as opposed to grouping everything together. 


### Clean Code 
I'd like to go back to make sure I've written my code efficiently. There are a few functions where I manipulate my data that could be more performative. I'd also like to experiment with cleaning the data differently - perhaps using the average levels for a1c and average time between visits isn't the best way to look at the data. 

### Feature Engineering 
I am going to continue to feature engineer to see if we've missed any important features that, when put together, impact a1c. 