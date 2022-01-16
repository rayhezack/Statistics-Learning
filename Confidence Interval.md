### Confidence Interval

In the passage, I will explain several concepts with regard to ***confidence interval***, including:

- What is confidence interval
- How to compute confidence interval
- What Does a Confidence Interval Reveal?
- T-test


#### What is Confidence Interval?

Confidence interval is  estimated range of values calculated from a subset of data that is likely to contain the actual value of population parameter. By contain the actual value, the true value population parameter should be above the lower confidence bound but below the upper confidence bound.
If we let <img src="https://render.githubusercontent.com/render/math?math=%5Ctheta%0A"> denote the unknown population parameter,
then LCB<=<img src="https://render.githubusercontent.com/render/math?math=%5Ctheta%0A"><=UCB.
 The inteval is also called *coverage probability*. 

**Understanding Confidence Inteval**

> Confidence intervals quantify the degree of uncertainty in the treatment
> effect. -- *Trustowrthy Online Experiment*

Confidence interval measures the degree of certainty or uncertainty in a sampling
method. When constructing a confidence interval, we usually use confidence
intervals of 95% or 99%.

So how to understand 95% confidence interval? As mentioned before, CI measures
the uncertainty. If we select samples randomly from the same population
100 times, and calculate confidence intervals for each sample. There will
be 95 of them that contain the population parameter, while the remaining 5
does not include it. From the perspective of A/B testing, 95% Ci means
that 95 confidence intervals contain the significant research effect. 

<img src="https://render.githubusercontent.com/render/math?math=%5Ccolor%7Bcrimson%7D%7B%5Cbf%7B%5Ctext%7BThe%20biggest%20misunderstanding%20of%20CI%20is%20that%2095%25%20of%20data%20from%20a%20given%20sample%0Afalls%20between%20the%20lower%20bound%20and%20upper%20bound.%0A%7D%7D%7D">


#### How to Calculate Confidence Interval?

To calculate the confidence interval, we need two elements:
- An unbiased estimate of population parameter
- The standard error of this estimate
![img.png](img.png)

There are a number of CIs:
- Confidence interval for population proportion
- Confidence interval for population mean
- Confidence interval for the difference in means
- Confidence interval for the difference in proportions

Though the population parameter can be different, the principle of 
calculating CI is completely same. Another thing needs to be noticed here
is how to determine the value of "a few", which we usually use z score.

There are two ways of determining the value of t when constructing the confidence interval.
- One approach is based on making the very strong assumption that the data are independent and identically distributed, and follow a normal distribution. In this case, Z score will follow a Student-t distribution with n-1 degrees of freedom. If we set k equal to the 1-(1-alpha)/2 
- Another approach is CLT(Central Limit Theory). The CLT states that the sample mean of sufficiently large number of identically distributed random variates will be approximately distributed.  The CLT also implies that the Z-score will be approximately norally distributed. 
  - CLT guarantees normality of random sample data even as long as the sample size is sufficiently large even if the individual data values have distributions that are not normal.


To demonstrate how to calculate the CI,
we use the following example: The specific definition of "smoker" used here (SMQ020) identifies 
a person as being a smoker if they self-report as having smoked 100 or more cigarettes in their lifetime.

**Question: We will calculate the proportions of smokers separately for females and for males. Initially we can compare these two prorportions and their corresponding confidence intervals.**

*Step 1: calculate the best estimate of population parameter*
```Python
# replace numeric code with text labels
import pandas as pd
import numpy as np

data = pd.read_csv("nhanes_2015_2016.csv")
data["SMQ020x"] = data["SMQ020"].replace({1:"yes",2:"no",
                                         7:np.nan,9:np.nan})
data["RIAGENDRx"] = data["RIAGENDR"].replace({1:"Male",2:"Female"})

# calculate the number of smokers in each sex segment
cross_tab = pd.crosstab(index=data["RIAGENDRx"],columns=data["SMQ020x"])
cross_tab
```
|~|no|yes|
|---|---|---|
|Female|2066|906|
|Male|1340|1431|

There are 2976 females and 2759 males.

Then we can calculate the proportion of male smokers and femake smokers
```Python
smoke_da = data[["SMQ020x","RIAGENDRx"]]
res = smoke_da.groupby("RIAGENDRx").agg({'SMQ020x':[np.mean,np.size]})
res
```
|~|mean|size|
|---|---|---|
|Female|0.304845|2976.0|
|Male|0.513258|2759.0|

*Step 2: Calculate Standard Error for Sample Proportions*
```Python
res["se"] = np.sqrt(res.mean*(1-res.mean)/res.size)
from scipy import stats

# male CI
alpha = 0.05
n = smo_table.loc['Male',"size"]
t_dist = stats.t(n-1)
t = t_dist.ppf(1-alpha/2)

lcb = smo_table.loc["Male","mean"]-t*smo_table.loc["Male","se"]
ucb = smo_table.loc["Male","mean"]+t*smo_table.loc["Male","se"]

# compute the CI for females
n = smo_table.loc['Female',"size"]
t_dist = stats.t(n-1)
t = t_dist.ppf(1-alpha/2)
female_lcb = smo_table.loc["Female","mean"]-t*smo_table.loc["Female","se"]
female_ucb = smo_table.loc["Female","mean"]+t*smo_table.loc["Female","se"]
```
Alternative to calculation of confidence interval is to directly to import
statsmodel package.
```Python
import statsmodels.api as sm

ci_res = sm.stats.proportion_confint(cross_tab["yes"],cross_tab["total"],alpha=0.05)
lcb_female = ci_res[0]["Female"]
lcb_male = ci_res[0]["Male"]

ucb_female = ci_res[1]["Female"]
ucb_male = ci_res[1]["Male"]

print(lcb_male,ucb_male)
print(lcb_female,ucb_female)
```
`0.49458749263718593 0.5319290347874418`

`0.2882949879861214 0.32139545615923526`


#### What Does a Confidence Interval Reveal?

Recall that confidence interval measures the degree of uncertainty
in Treatment effect, CI can assis in deciding whether the research
result is significant or not. Take the two confidence intervals above
as an example, we can tell whether there is a significane difference
between proportion of male smokers and that of female smokers. It is 
easily found that the two confidence intervals do not overlap, indicating
that the population proportion of male smokers is significantly different
from that of female smokers.

**Note: we can infer the significant difference by the fact that
the two CIs do not overlap, but we cannot infer insignificance by 
two confidence intervals that do not overlap.**

The question here is that we have no idea as for how different the
two population proportions might be. To address this, we can construct a
confidence interval for the difference between the two population proportions.

```Python
# calculate the best estimate
d = female_p - male_p
# calculate the standard error for the difference in population pros
se_diff = np.sqrt(female_se**2+male_se**2)
# get the CI
lcb = d-2*se_diff
ucb = d+2*se_diff
print(d)
print(lcb,ucb)
```
`-0.20841304163963553`\
`(-0.23384976280226777, -0.1829763204770033)`\
Interpreting the confidence interval, we can know that wit 95% confidence,
the difference in smoking rate between male and female in populaiton is
estimated to be somewhere between -0.23 and -0.18. The range is consistent
with the observed result.


**What is T-test**

Confidence intervals are conducted using statistical methods, 
such as a t-test. A t-test is a type of inferential statistic 
used to determine if there is a significant difference between 
the means of two groups, which may be related to certain features. 
Calculating a t-test requires three key data values. They include 
the difference between the mean values from each data set 
(called the mean difference), the standard deviation of each group, 
and the number of data values of each group.


