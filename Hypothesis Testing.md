## Hypothesis Testing

In the passage, I will summarize knowledge about hypothesis testing and
learning notes of books, such as *Trustworthy Online Controlled Experiment*.
The main topics include:
- What is hypothesis testing
- How to conduct hypothesis testing
- Primary statistical tests
- What is wrong with multiple hypothesis tests?


### What is Hypothesis Testing?
Hypothesis testing is a kind of inferential statistics in which an analyst
assesses the plausibility of hypothesis by using sample data.

###  How hypothesis testing works?

In hypothesis testing, analysts randomly select samples from population and use
statistical tests to examine the plausibility of the null hypothesis and 
alternative hypothesis. The type of statistical test depends on the business
objective of our task. For example, we may use t-test to examine the plausibility
of hypothesis if our goal is to identify whether there is a treatment effect
between treatment group and control group. Or we may use chi-square test
to confirm the independence of two metrics/variables if we aim at confirming
the association between the two objects. 

When doing hypothesis test, we usually assume the equality
between population parameters. For example, we may assume that the *user
engagement* will be the same even if we launch a new feature into our product;
the alternative hypothesis will be the user engagement is different from before.

### How to conduct hypothesis testing?
There are four primary steps to implementing hypothesis test:
- State null hypothesis and alternative hypothesis on the condition of 
business objective
- Formulate an analysis plan, outlining how to evaluate the sample data.
what statistical test we will use
- Examine the assumptions of hypothesis test and carry out the plan to 
analyze the data 
- Make decision based on the p-value of tests, either accepting or rejecting
null hypothesis.


**Example of Hypothesis Testing**

91 out of 247 (36.8%) sampled parents of black children report that their 
child has had some swimming lessons. 120 out of 308 (38.9%) sampled parents 
of Hispanic children report that their child has had some swimming lessons. 
Is there a significant difference between the population proportions of parents of black children and parents of Hispanic children who report that their child has had some swimming lessons?

Population: parents with children

Parameter of Interest: the difference in population proportions of parents(Hispanic/Black)

*Step 1: State hypothesis*
- Null Hypothesis: the population proportion of parents with hispanic children
who had swimming lessons is not significantly different from that of parents with black children.
- Alternative Hypothesis: the population proportion of hispanic parents with children taking swimming lessons is 
significantly different from that of black parents with children taking swimming lessons.

*Step 2: Formulate analysis plan*

Since the objective is to test whether there is a significant difference 
in population proportions, we need to a kind of statistical test that compares
the proportion of the two groups. T-tets is a good choice here, but we need to 
check the assumptions of t-test.

*Step 3: Check assumptions*
Assumptions:
- The data are collected from representative, randomly selected portion of 
the total population.  
- The sample data should follow normal distribution.
- Homogeneity. The two sample data have the approximately equal variance.
- The two samples should have enough sample size.
```Python
# calculate the sample variance for the two groups
p1 = round(91/247,3)
p2 = round(120/308,3)
var_black = p1*(1-p1)
var_hispanic = p2*(1-p2)
print(var_black,var_hispanic)
```
`0.232576, 0.2379`
The two variances are highly similar, indicating that the groups have
approximately equal variance. Also the two sample sizes, 247 and 308, allow
us to rely on central limit theorem to state that the two samples have
normal distribution. Let's move onto the analysis seciton.

*Step 4: calculate test statistics and make decisions based on p-value*

test-statistics: <img src="https://render.githubusercontent.com/render/math?math=%5Cfrac%7B%5Chat%7Bp_1%7D-%5Chat%7Bp_2%7D-null%7D%5Csqrt%7B%7B%5Chat%7Bp%7D*(1-%5Chat%7Bp%7D)*(%5Cfrac%7B1%7D%7Bn_1%7D%2B%5Cfrac%7B1%7D%7Bn_2%7D)%7D%7D">

```Python
import numpy as np
from scipy import stats
# calculate test statistics
phat = (91+120)/(247+308)
phat_var = phat*(1-phat)
t_test = (p1-p2)/np.sqrt(phat_var*(1/247+1/308))
# calculate p-val
pval = 2*scipy.stats.norm(0,1).cdf(t_test)
print(pval)
```
`0.6093128715165157`
We need to accept the null hypothesis because p value is greater than 0.05.
So the population proportion of hispanic parents with children who had swimming
lessons is approximately the same as that of black parents with children who
had swimming lessons.


### Primary Comparison Tests
To determine the statistical test that we will use, we should consider
the prerequisite assumptions and number of groups being compared:
- Assumptions:
  - **Independece of observations:** the observations/variables included in our test
  are not correlated with each other. For example, multiple measurements of the same experiment
  subject are not independent, such as longitudinal studies. But measurements of
  different experiment subjects are independent.
  - **Normality.** Data should follow normal distribution. 
  - **Homogeneity of variance.** the variance within each group being compared
  is similar.
- Number of Groups being compared
- Number of metrics being compared
  ![img_2.png](img_2.png)

#### T-Test

T-test is a kind of parametric inferential statistical method used to compare the means
between two groups(two-sample t-test) or the sample mean with the specified value(one-sample t-test).
If a dataset follows a normal distribution with known population mean but unknown 
variance, t-test should be used. 

**Takeaways of T-test:**
- T-test is a parametric statistical test, meaning that it makes explicit assumptions
about the dataset.
- In T-test, test statistic follows a t-distribution under the null hypothesis.
- Compared with Z-test, which is appropraite for large sample, t-test can be used in
small dataset(n<=30).
- T-tets has different types based on the number of samples
  - One sample t-test compares the sample mean with the hypothesized value
  - Two sample t-tets compares the means of two independent groups.
  - Paired t-test compares the difference between the pair of dependent variables.

##### One Sample T-test
One sample t-tets compares the sample mean with the hypothesized value. For example,

One sample t-test formula:
<img src="https://render.githubusercontent.com/render/math?math=%5Cfrac%7B%5Cbar%7BX%7D-%5Cmu%7D%7B%5Cfrac%7BS%7D%7B%5Csqrt%7Bn%7D%7D%7D">
The statistic tries to measure the how far away the sample statistic is from
the hypothesized value in terms of standard errors. The further away our sample statistic is, the less confident we will 
be in our null hypothesized value.

**Lab of one sample t-test**

In previous years 52% of parents believed that electronics and social media was the cause of their teenager’s lack of sleep. Do more parents today believe that their teenager’s lack of sleep is caused due to electronics and social media?
Now we randomly sampled 1018 parents from population, and 56% of parents believe that teenager's lack of sleep is caused by electronics and social media.

- Null: p = 0.52
- Alternative: p != 0.52
```Python
import pandas as pd
import numpy as np
from scipy import stats
import statsmodels.api as sm

# calculate the t statistics
n = 1018
p = 0.56
pnull = 0.52
se = np.sqrt(p*(1-P)/n)
tstat = (p-pnull)/se

# calculate p value
pval = 1-stats.norm(0,1).cdf(tstat) # one-sided test
print(pval)
```
Alternative method is to directly apply proportions_ztest
```Python
zstat,pval = sm.stats.proportions_ztest(phat*n,n,pnull,alternative="larger")
print(pval)
```
0.005316510991822442. Based on the pvalue, We have sufficient evidence against
the null hypothesis. The proportion should be larger than 0.52 today.

##### Two Sample T-Test

Two sample t-test compares the means of two independent groups. For example, we launch
a new feature into product and want to examine the feature's impact on revenue. After
collecting data and randomly selecting samples from population into treatment and control,
we can compare the means of two groups to confirm the research effect.

**Lab of Two Sample T-test**

Considering adults in the NHANES data, do males have a significantly higher 
Body Mass Index than females?
```Python
# first we calculate the point estimate for the difference of BMI
filepath = "../data/nhanes_2015_2016.csv"
df = pd.read_csv(filepath)

df["RIAGENDRx"] = df["RIAGENDR"].replace({1:"Male",2:"Female"})
res = df.groupby("RIAGENDRx").agg({"BMXBMI":[np.mean,np.size,np.std]})
res.columns = ["mean",'size','std']
res
```
![img_3.png](img_3.png)

Here, the equal variance assumption is violated, so I will use the unpooled
approach to calculate the test statistic.
![img_4.png](img_4.png)
```Python
dif = res.loc["Female","mean"] - res.loc["Male","mean"]-0
var1 = res.loc["Female","std"]**2
var2 = res.loc["Male","std"]**2
n1 = res.loc["Female","size"]
n2 = res.loc["Male","size"]
# calculate tstat and pval
tstat = dif/np.sqrt(var1/n1+var2/n2)
pval = stats.t(n1+n2-1).cdf(tstat)
print(pval)
>0.9999999998027931
```
Alternative method is to use `ttest_ind`
```Python
import statsmodels.api as sm
tstat,pval,df = sm.stats.ttest_ind(mdata["BMXBMI"],fdata["BMXBMI"],alternative="larger",usevar="unequal")
print(tstat,pval,df)
>-2.608814433503646 0.995060976960757 173.60922650001183

# scipy package
tstat,pval = stats.ttest_ind(mdata["BMXBMI"],fdata["BMXBMI"],alternative="greater",equal_var=False)
print(tstat,pval)
>2.608814433503642,0.995060976960757
```
Based on the p value, we should accept the null hypothesis, indicating that
the BMI of males do not have significantly higher BMI than females.


##### Paired t-test

Paired t-test is used to compare the difference between dependent variables
for the same subject. For example, a measurement of patients is taken before
and after experiment.

- Null Hypothesis: there is no significant difference between the two dependent
variables
- Alternative Hypothesis: there is a significant difference between the two dependent variables.

**Assumptions:**
- Differences between the two dependent variables follows a normal distribution
- Differences between the two dependent variables should not have outliers
- Observations are randomly selected from the same population

For paired t-tets statistics, the formula is the same as one sample t-test:

<img src="https://render.githubusercontent.com/render/math?math=%5Cfrac%7B%5Cbar%7BX%7D_1-%5Cbar%7BX%7D_2%7D%7B%5Cfrac%7BS_d%7D%7B%5Csqrt%7Bn%7D%7D%7D">

**Example of Paired t-test**

An instructor wants to use two exams in he classes
next year:This year, she gives both exams to the students. She
wants to know if the exams are equally difficult and wants to check
this by looking at the difference between scores. If the mean difference
is close to zero, she will make a conclusion that the exams are equally difficult.

Based on the information, we can state the following hypothesis:

- Null: the two exams are equally difficult
- Alternative: the mean difference of two exams is statistically different

```Python
import pandas as pd
import numpy as np
# prepare the data
student = ["Bob","Nina","Tim","Kate","Alonzo","Jose","Nikhil","Julia","Tohru","Michael","Jean","Indra",
          "Susan","Allen","Paul","Edwina"]
exam_score1 = [63,65,56,100,88,83,77,92,90,84,68,74,87,84,71,88]
exam_score2 = [69,65,62,91,78,87,79,88,85,92,69,81,84,75,84,82]
data = pd.DataFrame({"student":student,"exam_score1":exam_score1,"exam_score2":exam_score2})
```
Calculate test statistic and p value of it
```Python
# calculate the difference between the two scores
data["difference"] = data.exam_score1 - data.exam_score2
dif_mean = data.difference.mean()
tstat = dif_mean/(data.difference.std()/np.sqrt(n1))
from scipy import stats
# construct a student t distribution with dof n-1
pval = 2*stats.t(n1-1).cdf(tstat)
>0.9717484484105116
```
Based on the p value, we need to accept the null, indicating that the two exams
are equally difficult.


#### ANOVA Test

ANOVA, which is also called analysis of variance, is a statistical test to
determine the means of more than two groups. 

There are two types of ANOVA test:
- One way ANOVA test, which uses one i
- Two way ANOVA test








