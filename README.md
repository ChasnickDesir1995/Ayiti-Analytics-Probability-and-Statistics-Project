# Ayiti-Analytics-Probability-and-Statistics-Project
On this project Ayiti Analytics want to know :
1. How many observations and variables are there in the dataset?
2.A Calculate the average age of the observations
On the whole dataset
On the whole male dataset
On all the female dataset
2.B Calculate
variance of the whole dataset,male and female
kurtosis of the whole dataset,male and female
skewness of the whole dataset,male and female
2.C Does the age distribution follow a normal distribution
3.Display the age frequency distribution
On the whole dataset
On the whole male dataset
On all the female dataset

[1]
0 s
12345678
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import re as re
import datetime as dt
from scipy.stats import chi2_contingency

Ayiti Analytics Probability and Statistics Project
[2]
30 s
12
from google.colab import files
uploaded = files.upload()

[3]
0 s
123456789
# Read the data using csv and excel
commune=pd.read_excel(r"commune.xlsx")
enroll = pd.read_csv(r"enroll.csv")
quest = pd.read_csv(r"quest.csv")
industry = pd.read_csv(r"industry.csv")
ord = pd.read_csv(r"ord.csv")
study_domain = pd.read_csv(r"study_domain.csv")
transaction = pd.read_csv(r"transaction.csv")
technology = pd.read_csv(r"technology.csv")
Use all the files to have a final datasets to have the following columns

questid
gender
age (age of applicant)
communeName (use data prep in commune file)
application_date (created at in quest file)
enroll_date (created_at in enroll file)
is_enroll (Yes/No) (use data prep in enroll file)
Education Level
Communication channels(hear_AA1)
Bootcamp Interest (after_AA)
Payement Date (use ord and transaction files)
Payed (Yes/No)
list Technologies as columns based (use get_dummies)
list Study domains (use get_dummies)
Job is formal
Have computer at home
Have internet at home
[4]
0 s
123
#concat ord and transaction
df=pd.concat([ord,transaction],axis=0)
df

[5]
0 s
12
# Rename columns
df.rename(columns={"created_at":"payment_date"}, inplace=True)
[6]
0 s
1234567891011
#Rename columns Created_at in Transaction files
transaction.rename(columns={"created_at":"moncash_date"}, inplace=True)

#Rename columns Created_at in Ord files
ord.rename(columns={"created_at":"credit_card_date"}, inplace=True)

#Rename columns Created_at in enroll files
enroll.rename(columns={"created_at":"Enroll_Date"}, inplace=True)

#rename columns created-at in quest file

[7]
0 s
12345
# Format to date
transaction.moncash_date = pd.to_datetime(transaction.moncash_date).dt.tz_localize(None)
ord.credit_card_date= pd.to_datetime(ord.credit_card_date).dt.tz_localize(None)
#enroll.Enroll_Date=pd.to_datetime(enroll.Enroll_Date)
#quest.Application_Date=pd.to_datetime(quest.Application_Date)
[8]
0 s
123
#Extract each values of "Study_domain" and place them into a separate Columns before Merge
study_domain1 = pd.get_dummies(data=study_domain[["key", "quest_id", "values"]], columns=['values'], prefix="", prefix_sep="")
study_domain2 = study_domain1.groupby("quest_id").sum()
[9]
0 s
123
#Extract each values of "Technology" and place them into a separate Columns before Merge
technology1 = pd.get_dummies(technology[["key", "quest_id", "values"]], columns=['values'], prefix="", prefix_sep="")
technology2 = technology1.groupby("quest_id").sum()
[10]
0 s
123
#Extract each values of "industry" and place them into a separate Columns before Merge
industry1= pd.get_dummies(industry[["key", "quest_id", "values"]], columns=['values'], prefix="", prefix_sep="")
industry2= industry1.groupby("quest_id").sum()
[11]
0 s
1234567891011121314
# Merge Tables by "quest_id"
result = pd.merge(quest,enroll,how="left",on="quest_id")

# Merge Tables by "user_id"
result1 =pd.merge(result,ord,how="left",on="user_id")
result2 = pd.merge(result1,transaction,how="left",on="user_id")

# create columns named "is_enroll"
result2["is_enroll"] = 'No'
result2.loc[~result2.Enroll_Date.isna(),["is_enroll"]] ="Yes"

[12]
0 s
12
# Drop Columns
result2.drop(["Unnamed: 0_x","modified_at_x","Unnamed: 0_y","expiry_date",'course_name','course_id_x','percentage_completed','completed_at','expired','is_free_trial','completed','started_at','activated_at','updated_at','Unnamed: 0_x','product_name', 'product_id','amount_dollars','amount_cents','subscription','quest_id_y','Unnamed: 0_y','modified_at_y','course_id_y','transaction_id'], axis="columns" , inplace=True)
[13]
0 s
12
#Drop columns
result2.drop(['coupon_code','coupon_id', 'affiliate_referral_code','status','study_domain'], axis="columns" , inplace=True)
[14]
0 s
12
#rename columns quest_id_x in result2 file
result2.rename(columns={"quest_id_x":"quest_id"}, inplace=True)
[15]
0 s
12
#merge tables on "quest_id"
result3=pd.merge(result2,study_domain2, how = 'left',on='quest_id')
[16]
0 s
12
#Merge tables
result4=pd.merge(result3,technology2, how = 'left',on='quest_id')
[17]
0 s
1234567891011
# Merge tables
#result5=pd.merge(result4,industry2, how = 'left',on='quest_id')

#Transform "Commune_Id" Values in lower Character before Merge
commune["Commune_Id"] = commune["Commune_Id"].str.lower()

#Rename "Commune_Id" in "commune"
commune.rename(columns = {"Commune_Id": "commune"}, inplace = True)

# Merge Result4 and Commune

[18]
0 s
12
#rename columns in result6 file
result6.rename(columns={"hear_AA_1":"communication_channel",'after_AA':"Bootcamp_Interest",'other_x':"Others_Study_Domain",'other_y':"Others_Technologies",'other':"other_industry",'Commune_FR':"commune_name"}, inplace=True)
[19]
0 s
123
#Drop columns
result6.drop(['commune',"department"], axis="columns" , inplace=True)
result6.columns
Index(['gender', 'dob', 'Application_Date', 'education_level', 'university',
       'current_employed', 'formal_sector_job', 'have_computer_home',
       'internet_at_home', 'communication_channel', 'Bootcamp_Interest',
       'quest_id', 'Enroll_Date', 'user_id', 'credit_card_date',
       'moncash_date', 'is_enroll', 'payed', 'Accounting', 'Computer Science',
       'Economics', 'Electrical Engineering', 'Law', 'Management', 'Medicine',
       'Statistics', 'Others_Study_Domain', 'Bash', 'Excel', 'Git', 'Java',
       'JavaScript', 'PHP', 'PowerBI or Tableau', 'Python', 'R', 'SQL', 'VBA',
       'Others_Technologies', 'commune_name', 'Departement'],
      dtype='object')
[20]
0 s
151112133414910687215
#Format in Date 
result6["dob"].replace({"3 aout 1977": "3/08/1977"}, inplace=True)
result6.dob = pd.to_datetime(result6.dob).dt.tz_localize(None)
result6.Application_Date = pd.to_datetime(result6.Application_Date).dt.tz_localize(None)

# remove timezone to those columns
#testdata['time'].dt.tz_localize(None)

# Add columns age
result6["age"] = (result6["Application_Date"] -result6["dob"])


[21]
0 s
12
#Merge Table
result7=pd.merge(result6,df[['payment_date','user_id']],how='left', on='user_id')
[22]
0 s
12
#Format in date
result7. payment_date = pd.to_datetime(result7. payment_date).dt.tz_localize(None)
[23]
0 s
123
#Drop columns
result7.drop(['user_id',"Departement","current_employed","university","dob","credit_card_date","moncash_date"], axis="columns" , inplace=True)
result7.columns
Index(['gender', 'Application_Date', 'education_level', 'formal_sector_job',
       'have_computer_home', 'internet_at_home', 'communication_channel',
       'Bootcamp_Interest', 'quest_id', 'Enroll_Date', 'is_enroll', 'payed',
       'Accounting', 'Computer Science', 'Economics', 'Electrical Engineering',
       'Law', 'Management', 'Medicine', 'Statistics', 'Others_Study_Domain',
       'Bash', 'Excel', 'Git', 'Java', 'JavaScript', 'PHP',
       'PowerBI or Tableau', 'Python', 'R', 'SQL', 'VBA',
       'Others_Technologies', 'commune_name', 'age', 'payment_date'],
      dtype='object')
1. How many observations and variables are there in the dataset
[24]
0 s
123
# here your codes
A=result7.shape
print(f'Observation Number is : {A[0]} and  Variables numbers are : {A[1]}' )
Observation Number is : 250 and  Variables numbers are : 36
2.A Calculate the average age of the observations
On the whole dataset
On the whole male dataset
On all the female dataset
2.B Calculate
variance of the whole dataset,male and female
kurtosis of the whole dataset,male and female
skewness of the whole dataset,male and female
2.C Does the age distribution follow a normal distribution
2.A

[25]
0 s
123
# Average age of the observation on the whole dataset
B=result7["age"].mean()
print(f" The average age on the role dataset is {B} years. ")
 The average age on the role dataset is 26.475409836065573 years. 
[26]
0 s
1234
# Average age of the observation on the whole male dataset
B_male=result7[result7["gender"]=="male"]
B_male_avg=B_male.age.mean()
print(f" The average age on the male dataset is {B_male_avg} years. ")
 The average age on the male dataset is 26.798994974874372 years. 
[27]
0 s
1234
# Average age of the observation on the whole female dataset
B_female=result7[result7["gender"]=="female"]
B_female_avg=B_female.age.mean()
print(f" The average age on the female dataset is {B_female_avg} years. ")
 The average age on the female dataset is 25.044444444444444 years. 
2.B

[28]
0 s
123
#variance of the whole dataset,male and female
C=result7['age'].var()
print(f'The variance of the whole dataset is {C}. ')
The variance of the whole dataset is 52.19280847331848. 
[29]
0 s
123
# 2.B-variance of the whole male dataset
D=B_male['age'].var()
print(f'The variance of the whole dataset is {D}. ')
The variance of the whole dataset is 50.474544439368564. 
[30]
0 s
123
# 2.B-variance of the whole female dataset
E=B_female['age'].var()
print(f'The variance of the whole dataset is {E}. ')
The variance of the whole dataset is 58.54343434343435. 
[31]
0 s
12
#Impost missing package
import scipy.stats as stats
[32]
0 s
123
#kurtosis of the whole dataset
F=stats.jarque_bera(result7.age.fillna(result7.age.mean())>10)
print(f'The kurtosis of the whole dataset is {F} ')
The kurtosis of the whole dataset is (11209.480138310126, 0.0) 
[33]
0 s
123
#kurtosis of the female dataset
G=stats.jarque_bera(B_female.age.fillna(result7.age.mean())>10)
print(f'The kurtosis of the female dataset is {G} ')
The kurtosis of the female dataset is (834.3953034979429, 0.0) 
[34]
0 s
123
#kurtosis of the male dataset
H=stats.jarque_bera(B_male.age.fillna(result7.age.mean())>10)
print(f'The kurtosis of the male dataset is {H} ')
The kurtosis of the male dataset is (12007.954350023785, 0.0) 
[35]
0 s
1234
#skewness of the whole dataset
from scipy.stats import skew
I=stats.skew(result7.age.fillna(result7.age.mean())>10)
print(f"The skewness of the whole dataset is {I}. ")
The skewness of the whole dataset is -5.72215801063269. 
[36]
0 s
123
#skewness of the male dataset
J=stats.skew(B_male.age.fillna(result7.age.mean())>10)
print(f"The skewness of the male dataset is {J}. ")
The skewness of the male dataset is -6.133942657479973. 
[37]
0 s
123
#skewness of the female dataset
K=stats.skew(B_female.age.fillna(result7.age.mean())>10)
print(f"The skewness of the female dataset is {K}. ")
The skewness of the female dataset is -4.5325979795746765. 
2.C

[38]
0 s
1
Age=(result7.age.fillna(result7.age.mean()))
[39]
0 s
1
Age
0      22.0
1      24.0
2      24.0
3      24.0
4      23.0
       ... 
245    27.0
246    30.0
247    27.0
248    28.0
249    27.0
Name: age, Length: 250, dtype: float64
[40]
0 s
12845679101112313
# Does the age distribution follow a normal distribution

#Import missing Package
from scipy.stats import shapiro
# normality test
stat, p = shapiro(Age)
print('Statistics=%.3f, p=%.3f' % (stat, p))
# interpret
alpha = 0.05
if p > alpha:

Statistics=0.852, p=0.000
Sample does not look Gaussian (reject H0)
[41]
0 s
16789101112543213
# Does the age distribution follow a normal distribution

#import missing package
from scipy.stats import normaltest
# normality test
stat, p = normaltest(Age)
print('Statistics=%.3f, p=%.3f' % (stat, p))
# interpret
alpha = 0.05
if p > alpha:

Statistics=53.281, p=0.000
Sample does not look Gaussian (reject H0)
[42]
0 s
1234567891011121314
# Does the age distribution follow a normal distribution

#import missing package
from scipy.stats import anderson
# normality test
result = anderson(Age)
print('Statistic: %.3f' % result.statistic)
p = 0
for i in range(len(result.critical_values)):
	sl, cv = result.significance_level[i], result.critical_values[i]

Statistic: 9.841
15.000: 0.567, data does not look normal (reject H0)
10.000: 0.646, data does not look normal (reject H0)
5.000: 0.775, data does not look normal (reject H0)
2.500: 0.904, data does not look normal (reject H0)
1.000: 1.075, data does not look normal (reject H0)
3.Display the age frequency distribution
On the whole dataset
On the whole male dataset
On all the female dataset
[43]
0 s
12345
# Create a function who can do a pivot table

def pivot(data="x"):
  gender_pivot=pd.pivot_table(result7,values="quest_id",index=["age"], columns=data , aggfunc='count')
  return gender_pivot
[44]
0 s
123
#Display the age frequency distribution On the whole dataset
WD1=result7.age.value_counts(normalize=True).to_frame()
WD1

[45]
0 s
123
#Display the age frequency distribution On the Male dataset
Male=B_male.age.value_counts(normalize=True).to_frame()
Male

[46]
0 s
123
#Display the age frequency distribution On the female dataset
female=B_female.age.value_counts(normalize=True).to_frame()
female

4. Can we say that the average age (24 years old) of the observations can be considered as the average age of the population likely to participate in this bootcamp. Justify your answer
5. Calculate the average age of participants for each communication channel
6. Display an age boxplot for each communication channel
7 .Is there a significant age difference between these groups
