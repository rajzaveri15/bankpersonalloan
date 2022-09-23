# bankpersonalloan
Analyzing Banking with regards to Personal Loans

#### Project Overview
In the world of Banking, Loan Acceptance has always been an area of interest. How likely is it that an individual will accept a loan? What are the different factors that go into whether or not someone will accept a loan? I wanted to answer these questions and also explore banking data to discover different breakdowns of education levels, family sizes, and other factors.

#### Dataset Overview
The dataset used had attributes of both numeric and boolean values. It consists of 5000 records of bank customers and their following attributes: ID (Customer ID), Age (Customer's Age), Experience (number of years of professional experience), Income (Annual Income of the customer), ZIP Code (Customer's ZIP Code), Family (Customer's family size), CCAvg (How much the customer spent with their credit cards on average per month), Education (value of 1 for undergrad, 2 for graduate, and 3 for advanced degree), Mortgage (The value of the customer's house mortgage), Securities Account (Does the customer have a Securities Account (1/0)), CD Account (Does the customer have a Certificate of Deposit (CD) Account (1/0)), Online (Does the customer use online banking facilities (1/0)), CreditCard (Does the customer use a credit card issued by the Bank (1/0)), and Personal Loan (Whether or not the customer accepted the loan offered (1/0)).

#### Data Pre-Processing
The first step was to load the data into the JupyterHub Notebook and into a dataframe. Along with that I also needed to import the libraries that I was going to use including pandas, numpy, matplotlib, and seaborn. I then needed to checked to see if there were any null or incorrectly negative values. I found negative values for the Experience attribute which was not correct since you cannot have negative years of experience. To fix this I replaced all of the negative values in Experience with the mean value of 20. The data was then ready for analysis!

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_excel("C:/Users/rajza/Documents/Portfolio Projects/Personal_Loan_Banking/Bank_Personal_Loan_Modelling.xlsx",1)
```
```python
df.isnull().sum()
sns.histplot(df2['Experience'])
```

![image](https://user-images.githubusercontent.com/114118047/191823078-3d3b5369-2522-45f7-bb12-d80dc3ff7bb0.png)

```python
df3 = df2.copy()
df3['Experience']=np.where(df3['Experience']<0, df3['Experience'].mean(), df3['Experience'])
sns.histplot(df3['Experience'])
 ```
 
 ![image](https://user-images.githubusercontent.com/114118047/191823498-d224971a-3070-47af-9fe2-ee3a76c8bc3f.png)

#### Data Analysis
The first step I took in analyzing the data was to find the correlations and construct a heat map between the dataset's important features. 

```python
plt.figure(figsize=(15,10))
sns.heatmap(df3.corr(), annot=True)
```

![image](https://user-images.githubusercontent.com/114118047/191824491-3a177794-5aa6-457d-8770-cc2d3db66638.png)

After looking at the heatmap, I discovered that both Age and Experience were very highly correlated with eachother. To avoid multicolinearity, I ended up dropping the 'Experience' attribute since it was not needed.

```python
df3 = df3.drop(['Experience'],axis=1)
```
The next step was to get a breakdown of the dataset from the perspective of education level. To do this I created a new column which takes the values of the Education attribute (1,2,3) and converts them to their category equivalents (Undergrad, Grad, Advanced).
 
```python
def edlevel(x):
   if x == 1:
       return 'Undergraduate'
   elif x == 2:
       return 'Graduate'
   else:
       return 'Advanced Degree'

df3['Ed Level'] = df3['Education'].apply(edlevel)
```
I then used this column to construct a pie chart to depict the breakdown of the dataset by education level.

```python
ed_level_breakdown = df3.groupby('Ed Level')['Education'].count()
plt.figure(figsize=(7,7))
plt.title("Breakdown of Degree Levels")
plt.pie(ed_level_breakdown, labels = ed_level_breakdown.index, autopct='%1.2f%%')
plt.show()
```
![image](https://user-images.githubusercontent.com/114118047/191826403-8fb1eb79-022b-4deb-921a-ab46a3290a65.png)

From the pie chart it is clear to see that customers holding only Bachelor's degree were the majority.

Next, I wanted to find out how many customers held which accounts. How many had Securities accounts or CD Accounts or both or none. To find this breakdown, I created a new column which used the boolean values of the Securities and CD Accounts attributes and presented them in words. The values of the new column were 'Holds Securities and Deposit', 'Holds only Securities', Holds only Deposit', 'Does not hold Securities or Deposit.'

```python
def Security_CD(row):
    if (row['Securities Account']==1) & (row['CD Account']==1):
        return 'Holds Securities & Deposit'
    elif (row['Securities Account']==0) & (row['CD Account']==0):
        return 'Does not hold Securities or Deposit'
    elif (row['Securities Account']==1) & (row['CD Account']==0):
        return ' Holds only Securites '
    elif (row['Securities Account']==0) & (row['CD Account']==1):
        return ' Holds only Deposit' 
```
```python
df3['Accounts Held']=df3.apply(Security_CD,axis=1)
```

![image](https://user-images.githubusercontent.com/114118047/191828075-f0a4c4e6-6555-4ffc-abd8-e334d589bba4.png)

From this pie chart it was easy to tell that that the majority of the customers did not have either of the two accounts.

The next step was to explore The effect of Education Levels and Income on the acceptance (or non acceptance) of a personal loan. To do this, I created a boxplot with the X-axis being education levels and the Y-axis being Income and the two different boxplots representing acceptance or non acceptance for each education level. 

```python
plt.figure(figsize=(20,10))
sns.boxplot(x=df3['Ed Level'], y=df3['Income'], hue = df3['Personal Loan'])
plt.title("Breakdown of Education Level compared to Income grouped by Personal Loan Accepted (brown) vs not Accepted (blue)", fontsize=20)
plt.show()
```
![image](https://user-images.githubusercontent.com/114118047/191829794-c7772519-77cb-415e-b77d-353bb89bd8d8.png)

To explore Personal Loan Acceptances' relations with other attributes, I dug a little deeper. I wanted to find out how many customers accepted or didn't accept a loan based on whether or not they held either a Securities or CD Account, whether or not they used online banking, and whether or not they had a credit card from the bank. To do this I created a few bar graphs.

```python
def plot(col1,col2,label1,label2,title):
    plt.figure(figsize=(12,8))
    sns.distplot(df3[df3[col2]==0][col1],hist=False,label=label1)
    sns.distplot(df3[df3[col2]==1][col1],hist=False,label=label2)
    plt.legend()
    plt.title(title)
 ```
 ```python
 for i in col_name:
    plt.figure(figsize=(15,5))
    sns.countplot(i, hue='Personal Loan', data = df3)
    plt.title("Personal Loan Accepted (brown) or not (blue) based on "+str(i),fontsize=15)
```
![image](https://user-images.githubusercontent.com/114118047/191832126-274aa9d6-4fff-422c-b249-6b9a0a8906b4.png)
![image](https://user-images.githubusercontent.com/114118047/191832141-3f92d9f5-4dec-49bb-9c7e-3c7e1eac94d0.png)
![image](https://user-images.githubusercontent.com/114118047/191832158-2c31c3c8-97c1-48f1-aa64-1464ee401f92.png)

From the graphs we can see that the Accounts held attribute had an impact on whether or not the customer accepted the loan. If they did not hold neither a Securities nor a CD Account, they most probably would not accept the loan. 

#### Statistical Tests
After doing some initial analysis on what affected a Personal Loan acceptance, I wanted to go further and actually make more scientific conclusions using statistics. I did a t-test on the different attributes and Personal Loan to see whether or not they had impacts on Personal Loan based on the p-values. 

```python
import scipy.stats as stats
def HypothesisTest(col2):
    arr1 = np.array(df3[df3['Personal Loan']==0][col2])
    arr2 = np.array(df3[df3['Personal Loan']==1][col2])
    t,p_value = stats.ttest_ind(arr1,arr2, axis=0)
    if p_value < 0.05:
        print(str(col2) + " has an impact on the Acceptance of a Personal Loan with a p-value less than 0.05: " + str(p_value))
    else:
        print(str(col2) + " does not have an impact on the Acceptance of a Personal Loan with a p-value higher than 0.05: " + str(p_value))
```

```python
HypothesisTest('Age')
HypothesisTest('Income')
HypothesisTest('Family')
HypothesisTest('CCAvg')
HypothesisTest('Mortgage')
```

What I found was that Income, Family Size, CC Avg, and Mortgage value all had an impact on Personal Loan with p-values of: 0.0, 1.4099040685673807e-05, 3.815683641946184e-159, 5.73034172157401e-24 respectively.

#### Conclusion
To conclude, this dataset told a few different stories and dissolved a few hypotheses I had at the beginning. One of them being that Education Level had no real impact on the Personal Loan acceptance. What did have an impact were the customers' ages and some of their banking details including Income, Average credit card spending per month and which accounts they held. One thing that was surprising was to find that the overwhelming majority of the customers did not have a Securities or CD Account. It was no surprise however that Income played a role in Personal Loan acceptance.

At the end of this project, I was able to complete the objective, gain insights and answer the questions that I had set out at the beginning. Finance has always interested me and as a Data enthusiast, I was glad to have the oppurtunity to explore this dataset. 
    
   

 
 
 
 
 
 
