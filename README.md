# Introduction

Data and the age of technology has nonetheless benefitted society a great deal. However, it is not to say that such advancements hasn't been accompanied by down-sides. One of which is identity fraud. The ability for fraudsters, scammers, and hackers to access information as they please is unprecedented and unsuspecting victims often succumb to methods such as phone calls, emails or even fake job postings! In an effort to prevent identity theft, using a data set acquired from [kaggle](https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction), we aim to create a machine learning model that can predict fraud vs non-fraud jobs based on several parameters.

[Executive Presentation](https://docs.google.com/presentation/d/1v73JqSy9JSMub6i1UreA3L4CD9q1c_3ZiC92hr3AVbY/)

## Outline

In data analysis, there are several important steps to follow to ensure a good result which allows us to break our project down into 5 segments:

1. Initial Analysis
2. Cleaning
3. Training
4. Validate
5. Final result

![outline](https://user-images.githubusercontent.com/100324759/182724518-8b1d7de3-b421-4c71-8e63-b355caee50da.PNG)

### Blueprint/Dashboard

To view the blue print of our project please visit [this link](https://docs.google.com/presentation/d/1v73JqSy9JSMub6i1UreA3L4CD9q1c_3ZiC92hr3AVbY/edit#slide=id.p)
To view the dashboard of our project please visit [this link](https://public.tableau.com/app/profile/ikyu.park/viz/capstone_16594856522150/Dashboard2?publish=yes)


## Initial Exploratory Data Analysis

Much of the data will be summarized with figures, refer to the cleaning [file](Uncleaned_Data_Analysis.ipynb) for more detail.

The data imported from the csv was a 17880x18 dataframe and contained various forms of data types as noted below:

![dtypes](https://user-images.githubusercontent.com/100324759/182724852-ebcc0c92-ab1c-4fc3-a90b-ebdbdc867353.PNG)

Some columns also had a staggering number of null values with 'salary_range' sitting at 15012 nulls

![count_null](https://user-images.githubusercontent.com/100324759/182725135-b4e05257-8133-49fe-ae52-68cb1d6e1a84.PNG)

Further exploration also revealed no correlation as shown in the heatmap

![heatmap](https://user-images.githubusercontent.com/100324759/182725759-5a461302-0b12-4871-810d-ff0fdd35fa83.PNG)

## Summaries

Columns job_id, description, and requirements were dropped for the summaries which will be explained in future sections.

The overall fraud rate is relatively low at 95.2% and varied greatly between requirements. As we can see from the figures below, fraud rates are the highest for jobs with the follow attributes: entry level jobs, high school level education, full-time, oil & energy industry jobs, and administrative/engineering jobs.

Aside from the just counts, we should also consider the percentages which gave us the following highest: Administrative - 18.9%, Oil & Energy 38.0%, Full-time 8.5%, and Highschool Ed. 2.0%.

![graphs](https://user-images.githubusercontent.com/100324759/182728992-071a9604-9e23-4d5b-a752-bef23f18eab8.PNG)

## Cleaning

The data had to be cleaned primarily in the columns with strings such as requirements, description, etc due to how long and complex they are. 

We first dropped the 3 columns job_id, salary, and title due to either too mnay nulls or all unique values. We were still left with a large amount of nulls so they were filled with 'not specified'. The location column was then split so that we would only keep the country for consistency. Further modifications include removing punctuation and various characters and then lower casing all characters using the following code
```
for col in clean_cols:
    dataset_df[col] = dataset_df[col].replace(r'[^a-zA-Z0-9\s]', '',regex=True)
    dataset_df[col] = dataset_df[col].replace(r'\s{2,}', '',regex=True)
    
string_cols = list(dataset_df.select_dtypes(include='object'))
for col in string_cols:
    dataset_df[col] = dataset_df[col].str.lower()
```
### Feature Encoding

The data was sub divided into nominal ('department', 'industry', 'function', 'Country') columns and ordinal columns ('employment_type','required_experience','required_education'). Given this, a target encoder was used on nominal columns while a labelencoder was applied to ordinal columns resulting in a much cleaner dataframe primarily made of integers. 
```
Targetenc = TargetEncoder()
for col in nom_cols:
    values = Targetenc.fit_transform(X = dataset_df[col], y = dataset_df['fraudulent'])
    dataset_df[col] = values[col]
```
![cleaned_dtypes](https://user-images.githubusercontent.com/100324759/182904854-016c9b4b-b6ec-41fe-af45-ee91833976ca.PNG)

### Tokenizing

To further clean our text columns, we continued to use NLTK and sklearn. First, the stop-words are removed using a lamba function on the columns and then stemming was applied. Finally, the columns were then combined into 2 columns representing all of the combined text and the combined text length.

![image](https://user-images.githubusercontent.com/100324759/182908382-a62976ee-2d7c-4c1d-b966-226e97c855d5.png)

## Machine Learning Model

Initialization of the ML model followed standard procedure with splitting the data accordingly into test and train data sets with a 20% test size. Followed by adding the scaler and scaling the data which gave us an overall std dev. of 1 and a mean of -1.

The neural network we decided to use is a decision tree based ML model called LGBM classifier. Having 250 interations/run and a relatively small learning rate takes a good amount of time but nonetheless was successful as we were able to achieve a accuracy of ~97%.

![model](https://user-images.githubusercontent.com/100324759/183107192-95a86829-07e0-49bb-af64-27d2ae7fe2b9.PNG)
![testing](https://user-images.githubusercontent.com/100324759/183107275-1f491596-5b7d-4dbb-a12f-37cf6644b834.PNG)
![tree](https://user-images.githubusercontent.com/100324759/183109205-499602fc-2315-4ec2-bbdf-a7188e756310.PNG)

Looking deeper into our ML model revealed that column_7 or combined text length was the most important feature and remained consistent even after using XGB instead of LGB. Interestingly, changing the learning rate for our ML model didnt appear to change the results overall in terms of accuracy. Trying various learning rates between 0.05 and 1 all yielded a relatively stable accuracy rating of > 94.5% with a learning rate of 1 being the lowest.

Taking a look at our confusion matrix, non-fraudulent jobs were predicted extremely accurately with only a < 1% of non-fraud being predicted as fraud. However, the number of fraudulent job posts predicted as non job fraud was 37.6%

![image](https://user-images.githubusercontent.com/100324759/183111594-d30f728e-97c4-4aa8-a5af-0e8ed2fd0a68.png)


# Conclusion

Overall, the machine learning model for detection job fraud proved to be a success. We were able to achieve an accuracy rating of > 98% on the model of choice and even with fluctuations in learning rate, the model proved to be quite resilient. We were also able to determine that certain sectors of the employment field were more susceptible to job fraud postings such as those with lower requirements (education/experience), administrative/eng positions, and also certain 'traits' were more identifiable with fraud jobs such as text length as mentioned in the ML model. While the project was a success, the limited availability of posting data in csv format is scarce. Future projects should look to scrape more data from web or find another source of data to improve the ML model as the sample size of fraudulent jobs is quite low. Future improvements can enable implementation of this model into various job boards to prevent identity theft.

