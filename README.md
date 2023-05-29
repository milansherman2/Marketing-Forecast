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
- ![Site Visits over the Last Three Years](https://github.com/milansherman/Marketing-Forecast/blob/main/Visit Volume.jpg?raw=true)
3.	A third issue was what part of the funnel to use as the target, i.e., visits, opened applications, submitted applications, etc. Recent conversion metrics for each channel could be used to extrapolate the rest of the funnel. 
- I started with using visits as the target.  Between the decrease in volume on our site and trying to make predictions at the individual channel level I wanted to choose a metric that would have enough data for the model to learn from.  As some of the channels were currently generating fewer than 5 bound policies per month, so having enough data was definitely a concern. However, the visits data was a bit noisy, so I decided to try opened applications at the target in the hopes that this metric would have enough data to train the model but with less noise than visits.  In fact, this turned out to be the case, and the error of the predictions were less than 5% in some cases.


