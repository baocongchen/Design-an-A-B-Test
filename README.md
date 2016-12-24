# Design an A/B Test

### by Buu Thong Tran

1.Choosing Metrics:
+ Invariant metrics: Number of cookies, Number of clicks
+ Evaluation metrics: Gross conversion, Retention, Net conversion

Table of baseline values

| Unique cookies to view page per day | Unique cookies to click "Start free trial" per day | Enrollments per day | Click-through-probability on "Start free trial" |	Probability of enrolling, given click |	Probability of payment, given enroll | Probability of payment, given click |
|:----------------------------------- | ------------- | ----- | ------- | ------- | ------- | ------ | ----- |
| 40000 | 3200 | 660 | 0.08 | 0.20625 | 0.53 | 0.1093125 |

2.Calculating Standard Deviation:
+ Gross Conversion
+ Retention
+ Net Conversion