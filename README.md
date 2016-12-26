# Design an A/B Test

### by Buu Thong Tran

## Experiment Design

1. Choosing Metrics:

The practical significance boundary for each metric, that is, the difference that would have to be observed before that was a meaningful change for the business, is given in parentheses. All practical significance boundaries are given as absolute changes.
Any place "unique cookies" are mentioned, the uniqueness is determined by day. (That is, the same cookie visiting on different days would be counted twice.) User-ids are automatically unique since the site does not allow the same user-id to enroll twice.

- Number of cookies: That is, number of unique cookies to view the course overview page. (dmin=3000)
- Number of user-ids: That is, number of users who enroll in the free trial. (dmin=50)
- Number of clicks: That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger). (dmin=240)
- Click-through-probability: That is, number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page. (dmin=0.01)
- Gross conversion: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. (dmin= 0.01)
- Retention: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout. (dmin=0.01)
- Net conversion: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. (dmin= 0.0075)

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

4. Choosing Duration vs Exposure: <br>
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

### 2. Effect Size Tests

| | Experimental group | Control group |
| --- | ------------------ | ------------- |
| Clicks | 17260 | 17293 |
| Enrollments | 3423 | 3785 |
| Payments | 1945 | 2033 |
| Gross Conversion | 0.19832 | 0.218875 |
| Net Conversion | 0.11269 | 0.11756 |

`p_pooled = (X_exp + X_col) / (N_exp + N_col)`
`se_pooled = sqrt(p_pooled * (1 - p_pooled) * (1/N_exp + 1/N_col))`
`p_diff = X_exp/N_exp - X_col/N_col`

I apply the above formula to calculate the lower bound and upper bound of the evaluation metrics.

#### Gross Conversion:

`p_pooled = 0.2086
se_pooled = 0.00437
p_diff = -0.020555
margin_err = 0.00437*1.96 = 0.0085652
lower bound = p_diff - margin_err = -0.0291
upper bound = p_diff + margin_err = -0.01198`

Since the interval does not contain 0, it is statistically significant, and the lower bound of the confidence interval is more negative than our minimum detectable effect, so it is practically significant.

#### Net Conversion:

`p_pooled = 0.11512
se_pooled = 0.003434
p_diff = -0.00487
margin_err = 0.003434*1.96 = 0.00673
lower bound = p_diff - margin_err = -0.0116
upper bound = p_diff + margin_err = 0.00186`

Since the interval includes 0, it is not statistically significant. The lower bound is less negative than our minimum detectable effect and therefore not practically significant.

### 3. Sign Tests

Gross conversion has 4 successes out of 23 days, so with the probability of "success" of 0.5, the two tailed p-value is 0.0026 which statistically significant
Net conversion has 10 successes out of 23 days, so with the probability of "success" of 0.5, the two tailed p-value is 0.6776 which is not statistically significant

## Summary

I do not use Bonferroni correction, because the evaluation metrics are positively correlated, so the Bonferroni correction can be conservative to it.

The results I get from both effective size tests and sign tests show that the change will practically and significantly reduce the gross conversion, but will not practically and significantly affect the net conversion rate.