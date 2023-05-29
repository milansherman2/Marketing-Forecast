# Marketing-Forecast
Deep Learning Time Series Model using Neural Prophet

<h2>Business Problem</h2> 
<p>To anticipate and plan for revenue, expenses, and cost per acquisition (CPA), we need to be able to generate the follwing metrics by marketing channel:</p>

- Site visits 
- Started applications 
- Submitted applications 
- Approved applications 
- Bound (purchased) policies 


As the pay structure varies by channel, e.g., cost per click, started app, bound policy, etc., we need to be able to forecast these metrics by channel to get an accurate CPA. For revenue it’s generally sufficient for the aggregated numbers across all marketing channels for the month to be accurate, so some error/variation for individual channels is acceptable if the aggregated numbers are accurate. However, the pay structure varies from channel to channel, there is still a need to try to minimize the error per channel to accurately predict costs.  The growth team had been generating this forecast manually, using historical data and anticipated changes in marketing spend for the following month. The goal was to develop a more sophisticated model that can be automated. 

<b>Methods</b>:

The first question to answer was how accurate we could make the model and what parameters we could control to achieve better accuracy.  Three factors were identified at the outset:
1.	Given that a 30 day forecast was the goal, should predictions be on a daily basis and aggregated, or just generate a single prediction for the next 30 days? 
- I decided to start with making daily predictions, for a couple of reasons.  First, making predictions at the same granularity as the data made the most sense.  More importantly, however,  making predictions at the daily level gave me more leverage in terms of tweaking the model to get accurate numbers for the month.  It shifts the problem from one of accuracy to one of distribution of errors.
2.	Another question was what data to train the model on.  Due to changes in marketing spend and other factors, there had been a significant shift in  site traffic.  
- I started by looking at traffic over the last 3 years.  Below is the trend for opened apps over the last 3 years, and there is clearly a shift to a new “normal” last September.  I did try generating predictions using all of the data, but the model learned the downward trend from prior to this time and the predictions were a gross underestimate, and many of them were even negative.  Thus, I decided to train the model using data from last September onward.  The downside to this approach is that the model cannot learn annual seasonality with less than a year’s worth of data.
![Site Visits over the Last Three Years](https://github.com/milansherman2/Marketing-Forecast/blob/main/Visit%20Volume.png)
3.	A third issue was what part of the funnel to use as the target, i.e., visits, opened applications, submitted applications, etc. Recent conversion metrics for each channel could be used to extrapolate the rest of the funnel. 
- I started with using visits as the target.  Between the decrease in volume on our site and trying to make predictions at the individual channel level I wanted to choose a metric that would have enough data for the model to learn from.  However, the visits data was a bit noisy, so I decided to try opened applications at the target in the hopes that this metric would have enough data to train the model but with less noise than visits.  In fact, this turned out to be the case, and the error of the predictions were less than 5% in some cases. </p>

<b>Hyperparameter Tuning</b>
<p>
There are a  number of parameters that can be adjusted in the Neural Prophet package, but I started with just a few to see what kind of performance I could get initially.  In general, there are two components to the Neural Prophet model: a trend component, which discerns an overall trend in the target over time, and seasonality component, which identifies repeated patterns over a period of time, i.e., day, week, or year.  The effectiveness of Neural Prophet in particular, and time series models in general, depends on their ability to tease apart these different components and model them separately. 
  </p>
  
The three parameters that I tuned to get the intial model were: 
1. Seasonality mode: this controls how the seasonal component of the model interacts with the trend component.  Possible values: 
- Additive seasonality (default): the seasonal component is added to the trend, so that it is assumed to have fixed effects across the time series.  This means that if the trend increases, the seasonal variation stays the same.
- Multiplicative seasonality:  the seasonal component is multiplied by the trend, so that the it is assumed that effects change with the trend across the time series.  This means that if the trend increases, the seasonal variation also increases, and vice versa.
2. Trend reg: this controls the strength of the regularization of the trend component of the model.  A higher value of this parameter results in a smoother trend with fewer fluctuations and more gradual changes over time.  
3.  Seasonality reg: similar to trend reg, this controls the strength of the regularization of the seasonal component of the model, resulting in smoother and more gradual adjustments to the seasonal component of the model.

After tuning these parameters I obtained the following results for a 30 day forecast.

<b>Results</b>:

Metric | Actual | Predicted | Difference | Percent Difference
--- | --- | --- | --- | ---
Visits | 43582 | 43762 | 180 | 0.4% 
Opened | 3414 | 3639 | 225 | 6.6%
Submitted | 2411 | 2671 | 260 | 10.8%
Approved | 549 | 664 | 115 | 20.9%
Bound | 172 | 193 | 21 | 12.2%

Observations:
1.	The target (opened applications) was off by less than 7%.  This is the most important metric for two reasons:

  a. It’s the basis for all the other metrics, which are derived using conversion rates.
  
  b.	It’s the only one the model can really be held accountable for.  

For example, the forecasted number of opened applications was off by less than 7%, but the number of approved applications was off by over 20%.  That error is due to the inaccuracy of the funnel conversion rates compounded from opened to approved application, i.e., submit rate and approval rate.

2.	Visits were much more accurate using open apps and dividing by the start rate (opened applications/visits) than trying to predict them directly.  This is due to both the accuracy of the opened applications predictions and the historical start rate.

3.	Predictions at the individual channel level we much less accurate than at the aggregate level.  For the purposes of this forecast, that was fine, but this represents an area to improve in future iterations as improvement at this level will help to tighten up the aggregated forecast and better predict marketing costs since the pay structure for each channel varies, and thus a more accurate forecast at the channel level will help us to better anticipate cost per acquisition (CPA).  

