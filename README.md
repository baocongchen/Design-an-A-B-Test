# Design an A/B Test

### by Buu Thong Tran

## Experiment Design

1. Choosing Metrics:

+ Invariant metrics: Number of cookies, Number of clicks

+ Evaluation metrics: Gross conversion, Retention, Net conversion

Table of baseline values:

| Unique cookies to view page per day | Unique cookies to click "Start free trial" per day | Enrollments per day | Click-through-probability on "Start free trial" |	Probability of enrolling, given click |	Probability of payment, given enroll | Probability of payment, given click |
|:----------------------------------- | ------------- | ----- | ------- | ------- | ------- | ------ | ----- |
| 40000 | 3200 | 660 | 0.08 | 0.20625 | 0.53 | 0.1093125 |

2. Calculating Standard Deviation: <br>

**Apply the formula below**  <br>
`sqrt(p*(1-p)*(N/n)/rate)`

+ Gross Conversion: 0.02023060414

+ Retention: 0.05494901218

+ Net Conversion: 0.01560154458

3. Calculating Number of Pageviews: <br>
I do not use Bonferroni correction in the analysis phase because the metrics are positively correlated, so Bonferroni correction can be conservative in this case. <br>
Then I use Evan's A/B Tools to calculate the sample size for each evaluation metric <br>
**alpha=0.05, beta=0.2**

- Gross conversion (dmin=1%, baseline conversion rate=0.20625): 25,835 clicks on "Start free trial"

- Retention (dmin=1%, baseline conversion rate=0.53): 39,115 enrolls

- Net conversion (dmin=0.75%, baseline conversion rate=0.1093125): 27,413 clicks on "Start free trial"

It takes too long to get 39,115 enrolls to perform A/B test, so I decide to use just gross conversion and net conversion as evaluation metrics.<br>

`Number of Pageviews = Clicks on "Start free trial" / Click-through-probability on "Start free trial"` <br>

- Number of pageviews in gross conversion case = 25,835/0.08 = 322,937.5 <br>
- Number of pageviews in net conversion case = 27,413/0.08 = 342,662.5 <br>

To use both gross conversion and net conversion as evaluation metrics, we need 342,662.5 pageviews, so the total number of pageviews we need for both experiment & control group are `342,662.5*2=685325` <br>

4. Choosing Duration & Exposure: <br>
There are 40000 pageviews per day, and I want to direct 50% of the traffic to the experiment, so the number of days needed to perform the experiment is `685325/20000 = 34.3` days. <br>

## Experiment Analysis

### 1. Sanity Checks

***Page Views***

| Experimental group | Control group |
|:------------------ | ------------- |
| 344660 | 345543 |

Fair experiment should gives us equal proportion between number of cookies in control and experiment groups, so I use 0.5 proportion as my point estimate.
- Standard Deviation = sqrt(0.5*0.5/(344660 + 345543)) = 0.0006018
- Margin of Error = 0.0006018*1.96 = 0.001179528
- Confidence Interval = [0.498820472, 0.501179528]
- Observed value = 345543/(344660 + 345543) = 0.50064
The observed value falls within the confidence interval, so it passes the sanity check.

***Clicks***

| Experimental group | Control group |
|:------------------ | ------------- |
| 28235 | 28378 |

- Standard Deviation = sqrt(0.5*0.5/(28378 + 28235)) = 0.0021014
- Margin of Error = 0.0021014*1.96 = 0.004119
- Confidence Interval = [0.495881, 0.504119]
- Observed value = 28378/(28378 + 28325) = 0.5005
The observed value falls within the confidence interval, so it passes the sanity check.

### 2. Effect Size Testss