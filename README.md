# Design an A/B Test

### by Buu Thong Tran

## Experiment Design

1. Choosing Metrics:

The practical significance boundary for each metric, that is, the difference that would have to be observed before that was a meaningful change for the business, is given in parentheses. All practical significance boundaries are given as absolute changes.
Any place "unique cookies" are mentioned, the uniqueness is determined by day. (That is, the same cookie visiting on different days would be counted twice.) User-ids are automatically unique since the site does not allow the same user-id to enroll twice.

+ Invariant metrics: Number of cookies, Number of clicks.

+ Evaluation metrics: Gross conversion, Retention, Net conversion.

Invariant metrics are metrics that are not expected to change between the control group and experimental group; therefore, they used in sanity checks. In this case, number of cookies and number of clicks are selected as invariant metrics because they are not impacted by the new design of "Start free trial". On the other hand, evaluation metrics are subject to change between the control group and experimental group, so they are used in effective size tests and sign tests to measure the impact of a change. The details of how I choose the metrics is presented below:

- Number of cookies: That is, number of unique cookies to view the course overview page. (dmin=3000). *This can be used as an invariant metric because the number of visitors is not impacted by the change of "Start free trial".*  
- Number of user-ids: That is, number of users who enroll in the free trial. (dmin=50). *This is not a good invariant metric because it is impacted by the experiment. This can be used as an evaluation metric; however I do not use it because the number of user-ids needs to be normalized, otherwise it may skew the result. Therefore, we use gross conversion metric as an alternative for number of user-ids*
- Number of clicks: That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger). (dmin=240). *This can be used as an invariant metric because clicks happen before visitors see the experiment, thus is not impacted from the experiment. For the same reason, it should not be used as an evaluation metric because we want to measure how the experiment impacts the metric.*
- Click-through-probability: That is, number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page. (dmin=0.01). *This should not be used as an evaluation metric because we want to measure how the experiment impacts the metric. While the metric can be used as an invariant metric because clicks happen before visitors see the experiment, it should not be used in this A/B test because its unit of measurement is not count but rate, that is the unit is different from that of the other two invariant metrics; the metric is based on clicks, so number of click instead of click-through-probability is a better choice for this A/B test.*
- Gross conversion: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. (dmin= 0.01). *This should not be used as an invariant metric because the number of visitors enrolled in the program is affected by the experiment. However, it is a good evaluation metric because it reflects the impact the experiment has on the number of students enrolled in the program.*
- Retention: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout. (dmin=0.01). *This should not be used as an invariant metric because the number of visitors remained enrolled after 14 day trial is impacted by the experiment. On the other hand, this can be used as an evaluation metric because it reflects the impact the experiment has on the number of students remained enrolled in the program after 14 day trial.*
- Net conversion: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. (dmin= 0.0075). *This should not be used as an invariant metric because the number of visitors remained enrolled  after 14 day trial is impacted by the experiment. On the other hand, this is a good evaluation metric because it reflects the impact the experiment has on the number of students remained enrolled in the program after 14 day trial.*

<br>
I will take a look at the gross conversion metric and net conversion metric. The gross conversion metric indicates whether or not we successfully lower the costs by introducing the screener, and the net conversion metric indicates how the change affects the revenues. <br>
In order to launch the experiment, we have to make sure that it will reduce enrollments by unprepared students **and** without signficantly reducing the number of students who complete the free trial and make at least one payment. That is the gross conversion metric needs to have a practically significant decrease, and the net conversion metric should not have a statistically significant decrease.

Table of baseline values:

| Unique cookies to view page per day | Unique cookies to click "Start free trial" per day | Enrollments per day | Click-through-probability on "Start free trial" |	Probability of enrolling, given click |	Probability of payment, given enroll | Probability of payment, given click |
|:----------------------------------- | ------------- | ----- | ------- | ------- | ------- | ------ | ----- |
| 40000 | 3200 | 660 | 0.08 | 0.20625 | 0.53 | 0.1093125 |

2. Calculating Standard Deviation: <br>

**Apply the formula below** <br>
`sqrt(p*(1-p)*(N/n)/rate)`
+ Gross Conversion: 0.02023060414

+ Retention: 0.05494901218

+ Net Conversion: 0.01560154458

Both gross conversion and net conversion use number of cookies as denominator, which is also unit of diversion. Therefore the analytic estimate is comparable to the emperical estimate. Retention does not have the same unit of diversion; its denominator is user-id, so its analytic estimate will not match its emperical estimate. 

3. Calculating Number of Pageviews: <br>
I do not use Bonferroni correction in the analysis phase, because according to the hypothesis, we look for a decrease in gross conversion and for a no decrease in the net conversion, so the use of Bonferroni correction is not relevant.<br>
Then I use Evan's A/B Tools to calculate the sample size for each evaluation metric <br>
**alpha=0.05, beta=0.2**

- Gross conversion (dmin=1%, baseline conversion rate=0.20625): 25,835 clicks on "Start free trial"

- Retention (dmin=1%, baseline conversion rate=0.53): 39,115 enrolls

- Net conversion (dmin=0.75%, baseline conversion rate=0.1093125): 27,413 clicks on "Start free trial"

It takes too long to get 39,115 enrolls (approximately 60 days if 100% of the traffic is used) to perform A/B test, so I decide to use just gross conversion and net conversion as evaluation metrics.<br>

`Number of Pageviews = Clicks on "Start free trial" / Click-through-probability on "Start free trial"` <br>

- Number of pageviews in gross conversion case = 25,835/0.08 = 322,937.5 <br>
- Number of pageviews in net conversion case = 27,413/0.08 = 342,662.5

To use both gross conversion and net conversion as evaluation metrics, we need 342,662.5 pageviews, so the total number of pageviews we need for both experiment & control group are `342,662.5*2=685325` <br>

4. Choosing Duration vs Exposure: <br>
There are 40000 pageviews per day, and I want to divert 60% of the traffic to the experiment, so the number of days needed to perform the experiment is 29 days. This duration is not too long, so it is reasonable to perform A/B test. Moreover, the experiment neither involves sensitive data nor harms anyone, so it is not extremely risky to perform this test<br>

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
- Observed value = 28378/(28378 + 28325) = 0.5005 <br>
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

`p_pooled = 0.2086`

`se_pooled = 0.00437`

`p_diff = -0.020555`

`margin_err = 0.00437*1.96 = 0.0085652`

`lower bound = p_diff - margin_err = -0.0291`

`upper bound = p_diff + margin_err = -0.01198`

Since the interval does not contain 0, it is statistically significant, and the lower bound of the confidence interval is more negative than our minimum detectable effect, so it is practically significant.

#### Net Conversion:

`p_pooled = 0.11512`

`se_pooled = 0.003434`

`p_diff = -0.00487`

`margin_err = 0.003434*1.96 = 0.00673`

`lower bound = p_diff - margin_err = -0.0116`

`upper bound = p_diff + margin_err = 0.00186` 

Since the interval includes 0, it is not statistically significant. The lower bound is less negative than our minimum detectable effect and therefore not practically significant.

### 3. Sign Tests

Gross conversion has 4 successes out of 23 days, so with the probability of "success" of 0.5, the two tailed p-value is 0.0026 which statistically significant <br>
Net conversion has 10 successes out of 23 days, so with the probability of "success" of 0.5, the two tailed p-value is 0.6776 which is not statistically significant 

### Summary

I do not use Bonferroni correction. We should use Bonferroni correction only if we were to launch the experiment when any metric would match our expectations because we want to increase the risk of Type I error. In this case, we look for a decrease in gross conversion and for a no decrease in the net conversion, so the use of Bonferroni correction is not relevant.
<br>
The results I get from both effective size tests and sign tests show that the change will practically and significantly reduce the gross conversion, but will not practically and significantly affect the net conversion rate.

### Recommendation

Based on the results that I get, I think the new design for "Start free trial" should not be used. The gross conversion metric is negative, statistically and practically significant, which means the new design has successfully decreased the number of users that cancel early. However the net conversion rate is neither statiscally nor practically significant, which means the new design has failed to increase the number of paid users. The confident interval also includes negative range which indicates that the experiment may negatively impact the revenue. This is the reason why we should not launch this experiment.

## Follow-Up Experiment

We want to make some changes that can positively impact the net conversion rate. First, we need to detect the patterns of those who are likely to cancel the course during the free trial period; then we approach them and give them a free one-on-one coaching session. I think students who cancel early tend to be either lack of experience with online learning or lack of motivation to keep going forward, so it is important for us to identify them, actively approach and give them orientation, and encourage them. The intervention may affect human resources, so the duration of a free coaching session should be limited to 15, 20, or 30 minutes depending on individual cases. I believe the intervention, if carried out effectively, can increase retention rate, so I would like to conduct an A/B test to verify my idea. I will randomly assign 50% of the visitors to control group and the other 50% to experimental group. 

`Null hypothesis: free one-on-one coaching session does not increase retention by a practically significant amount.`

`Unit of diversion: user-id. Because a free one-on-one coaching session is given not to visitors but to those who are in the free trial period, and we want the experiment to be stable, that is we want to make sure only those in experimental group receive a free one-on-one coaching session.` 

`Invariant metric: the number of user-id. Because the experiment does not impact this metric`

`Evaluation metric: retention. If the retention is practically and statistically significant, we can conclude that the launch of the experiment will increase the revenue.`

Then we compare the results from experimental group and control group to see whether we should launch the experiment or not. If retention is positive and practically significant, we can launch the experiment.

## Reference
- Ideate and Hypothesize https://help.optimizely.com/Ideate_and_Hypothesize
- Evan's A/B Tools http://www.evanmiller.org/ab-testing/sample-size.html
- GraphPad Software http://graphpad.com/quickcalcs/binomial1.cfm