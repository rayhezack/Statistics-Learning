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



