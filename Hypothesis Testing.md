### Hypothesis Testing

In the passage, I will summarize knowledge about hypothesis testing and
learning notes of books, such as *Trustworthy Online Controlled Experiment*.
The main topics include:
- What is hypothesis testing
- How to conduct hypothesis testing
- Primary statistical tests
- What is wrong with multiple hypothesis tests?


#### What is Hypothesis Testing?
Hypothesis testing is a kind of inferential statistics in which an analyst
assesses the plausibility of hypothesis by using sample data.

####  How hypothesis testing works?

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

#### How to conduct hypothesis testing?
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


#### Primary Statistical Tests


