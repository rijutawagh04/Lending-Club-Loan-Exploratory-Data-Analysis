
# Lending-Club-Loan-Exploratory-Data-Analysis

### Lending Club operates an online lending platform that enables borrowers to obtain a loan, and investors to purchase notes backed by payments made on loans. Lending Club is the world's largest peer-to-peer lending platform

# Part 1: Data Download

Our first step is to get the Lending club dataset from the Lending Club web site.
To download the data that is available publicly from lending club site. https://www.lendingclub.com/info/download-data.action

The link contains files for complete loan data for all loans issued through the time period stated, including the current loan status (Current, Late, Fully Paid, etc.) and latest payment information


The Data Dictionary attached in extras folder includes definitions for all the data attributes included in the Historical data file and the In Funding data file.

The data is divided into multiple files based on the year in which loan was issued. To download multiple files in python we first need to get the URLs of all the files. For this we use urllib3 library. File name of all the files is fetched from the a hidden div variable. Hidden div for Accepted loan data is ‘loanStatsFileNamesJS’. This variable contains all the file names separated by ‘|’. From this names we form the URL and download the file using requests.get(url) method. Then we extract the downloaded zip files using the extractall() function  zipfile library.

A single function is written which can download accepted data files from 2007-2016 as .csv. All the csv files will then be concatenated to a single Concatenated.csv file. The data will be extracted and downladed in a folder Data. Further we read these files in loan Dataframe.

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/downloadnew.PNG)

# Part 2: Missing Value Handling

A. Checking for null values in the column and if the column contains more than 80% missing values, drop the column. Drop the columns which don't give any useful information for analysis.

I have dropped the following columns:
id - Randomly genereated Unique Identification Number 
member_id - randomly generated field by identification purposes
url
adesc
zipcode- since it contains onlky 3 digits and we have address state as another column
out_prncp - Gives future information

https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/gradetrends.PNGout_prncp_inv - Gives future information
total_pymnt_inv - Gives future information

Columns Updated and cleaned:
•	emp_length – As per the Data Dictionary, emp_length should have only values ranging from  0 to 10, whereas there were few records with value of “10+ years” and “<1 year”, which we replaced with 10 and 0 respectively. 

•	Title – Title is the reason for a person to borrow money. There are various categories based on different reasons provided by the users; since blank means they haven’t specified the reason for borrowing the money, we assumed that it was something they didn’t wanted anybody to know and had some personal reasons, so I assigned them the “Unknown” category to such rows.

•	mths_since_last_delinq – This field shows the number of months passed by since a person was marked delinquent. Here, I made use of another column which is correlated to this column i.e. delinq_2yrs. If the value in delinq_2yrs = 0, that means there has been no delinquencies recorded for a person in the last 2 years. So, making use of this analysis, I replaced all the empty data of mths_since_last_delinq which has delinq_2yrs as 0, as 24 (resembling 2 years).

•	mths_since_last_record – This field indicates the number of months passed by since a person had any public record. This value is provided by the Lender’s based on the data they have. All public record is noted and present in their database. Since, the field is left blank means, that a person doesn’t have any public record yet. So, I marked it as -1 to show that a person doesn’t have any public record.
•	annual_inc – Annual income is empty just for 4 records in the data. On analyzing I found that all these 4 records don’t have any employer, which means that they don’t earn so we replaced all blanks by 0.
•	delinq_2yrs – delinq_2yrs column has maximum of 39 and minimum of 0. On reading the data, I found that all the empty columns are for people who are not valid and does not meet the credit policy, so I replaced them by the mean.

•	revol_util – These are the percent values, so I first stripped all the “%” and then replaced it by the mean percentage 


•	delinq_amnt – 99.98% of the data is zero. Only negligible records have any other values, so I replaced it by zero
•	pub_rec_bankruptcies – All the empty rows are the ones which do not meet the credit policy and all are non-verified. With only negligible blanks, I replaced it by mean.


•	tax_liens – These are the same 29 empty records as pub_rec_bankruptcies and so we processed a new column as “derived_tax_liens”.
•	Interest_rate – Stripped the “%” and converted object datatype to float , so that analysis can be done on top of that 
•       term-replaced missing value with max term period(i.e. 60)
•       replace missing values for home_ownership with OTHER



# Part 3: Data pre-processing 

Converting ‘issue_d’ to datetime format using pd.to_datetime
I am creating new columns in the dataframe to store the below values:

•	Issue month as issue_month
•	Issue year as issue_year
Derived Variables:
I have derived the following variable

credit_age: This variable shows the duration since when the user is using credit. It is calculated by subtracting the first date when credit was checked from the date when credit/loan was issued.

# Part 4: Exploratory Data Analysis
# 1.Analysis 1: Loan volume trends by loan grade and year. Loan grade tells us how riskier the loan is. Loan grade A is least risky  and Loan G is the highest.
There is rapid increase in Loan B and E grade. Grade will be an important parameter to determine if the person is going to default loan.

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/gradetrends.PNG)

# 2.Analysis 2: Analysis of trends in loan amount vs year
Increase in loan amount issued rapidly. There was a downfall during 2008-2010 due to economic depression.

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/timeseries.png)

 Loan metrics by state showing the following:
1. Average balance per borrower
2. Average Employment term per borrower
3. Average annual income per borrower

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/Map.png)


![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/map2.PNG)

# 3.Analysis 3: Important to determine who will pay loan and who will default
Meaning for each loan status-https://help.lendingclub.com/hc/en-us/articles/215488038

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/loan_status.PNG)

Predicting default loans. From the above analysis,only the Fully Paid and Charged Off values describe the final outcome of the loan. The other values describe the loans that are still going on, and even though some loans are late on payment we cant classify them as Charged Off.
Also, while the default status resembles the Charged Off Status, in Lending Club's eyes, loans that are charged off have essentially no chance of being repaid while default ones have a small chance. Therefore, we should use only samples where the loan_status column is 'Fully Paid' or 'Charged Off'.
We are not interested in any statuses that indicate that the loan is ongoing or in progress, because predicting something in progress doesn't tell us anything.
Since we are interested in being able to predict which of these 2 values a loan will fall under, we can treat the problem as binary classification.
Let's remove all the loans that don't contain either 'Fully Paid' or 'Charged Off' as the loan's status and transform the 'Fully Paid' values to 1 for the posiive case and 'Charged Off' values to 0 for the negative case.

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/pie_chart_loan_status.PNG)

Conclusion: Significant number of borrowers in our dataset paid off their loan. 79.81% of loan borrowers paid off amount borrowed, while 20.19% unfortunately defaulted.

# 4.Analysis 4: Wordcloud of purpose for which the loans were taken
![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/word_cloud_loantitle.PNG)

Wordcloud showing maximum loan amount by state. People from California and New York took maximum number of loans
![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/word_cloud_state.PNG)




Wordcloud showing purpose for which loan was taken
![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/word_cloud_purpose.PNG)


Distribution of employment length for issued loans
![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/empLength.PNG)

  
The maximum no of issued loans were for people having employment length geater than or equal to 10.

# 5.Analysis 5:
I want to explore how various features affect interest rate so that they can be used to determine interest rate once the person is granted loan. We already have the accepted loan dataset and we can predict interest rate based on few variables. Thus, we will check which features are important to predict interest rate. So I need to get some basic statistics.

Next, I will explore how all variables (loan amount, term, grade, employee length, home ownership, annual income, issue day, purpose, state, application type) affect interest rate.

It turns out that loan amount, employee length, annual income, home ownership,state and issue month do not affect the interest rate much.
The term, grade, purpose and application type would affect the interest rate to some extent.
Thus, these variables can be used to predict interest rate. Lets take a look at these variables. 

# 1.FICO
FICO scores are a credit score, or a number used by banks and credit cards to represent how credit-worthy a person is. A minimum FICO score of 600 is required in order to grant  a loan.

When a borrower applies for a loan, Lending Club gets the borrowers credit score from FICO- they are given a lower and upper limit of the range that the borrower's score belongs to, and they store those values as fico_range_low, fico_range_high.

After that , any updates to the borrowers score are recorded as last_fico_range_low and last_fico_range_high

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/last_mean_ficovsint_ratescatter.PNG)

CONCLUSION: Borrowers with high FICO scores tend to get lower interest rates on mortgages than borrowers with low credit scores. A credit score of 740 or higher qualifies for the best interest rates from most lenders. Thus, we can infer that interest rate is affected by FICO score.

# 2.Term

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/termvsint_rate.PNG)

CONCLUSION: The interest rate is more for a term of 60 months which is ~13-19% whereas for a term of 36 months, it is ~9-14%. Thus, interest rate is affected by the term

# 3.Employment Length
![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/emp_lengthvsint_rate.PNG)
CONCLUSION:The interest rate is more for employment length of 10years 
 

# 4.Loan Amount
![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/loanamntvsint_rate.PNG)
CONCLUSION: Higher the loan amount, higher will be the interest rate.

# 5.Annual Income
<<<<image>>>

# 6.Home ownership

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/homeownvsint_rate.PNG)

# 7.Purpose![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/purposevsint_rate.PNG)

# 8.Application Type
![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/appltypevsintrate.PNG)


# 9.Debt to Income Ratio

Debt to income ratio represents the total amount of debt payments the borrower owes each month excluding mortgage (e.g. credit cards, student loans, car loans) divided by the stated monthly income. LendingClub only allows borrowers on their marketplace if DTI is < 35%¶
Interest rate increases with increase in dti
![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/dtivsintrate.PNG)


# 10.Address State
![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/statevsintrate.PNG)


# CONCLUSION:It's very likely that you can get low interest rate if the term is 36 months, dti is low, the grade is low, the purpose is one of educational, car or credit card, the state is Missouri, and the type is "individual"!


# Analysis:Attempt to predict the interest rate charged to a group/cluster of loans.

### Deciding how many groups to form based on Eucledian distance

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/noofgroupsplot.PNG)

This tells us that we need to form 4 groups.


There are 4 groups Medium, Low, MedHigh, High for each of the columns in the dataset. The plots above show annual income vs other features. People with annual income between ~100000 and 130000 belong to group1. People with annual income between ~140000-180000 belong to group2.  People with annual income between ~200000 and 300000 belong to group3. People with annual income > 400000 belong to group4 and are labeled as high.  

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/cluster_fico.PNG)

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/cluster_loanamnt.PNG)

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/cluster_term.PNG)

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/cluster_totalaccount.PNG)

![alt tag](https://github.com/rijutawagh04/Lending-Club-Loan-Exploratory-Data-Analysis/blob/master/final/images/clusterdti.PNG)


## Some key observations from the above plots:
1. dti is less for people with high annual income.
2. Most number of people in the dataset have annual income >400000 and thus qualify to apply for loan
3. Annual income increases with increase in employment length
4. delinq_2yrs is lower for people with annual income>=400000 and higher for people with annual income<=300000











 







