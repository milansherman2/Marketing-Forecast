# Marketing-Forecast
Deep Learning Time Series Model using Neural Prophet

<h2>Business Problem</h2> 
<p>To anticipate and plan for revenue, expenses, and cost per acquisition (CPA), we need to be able to generate the follwing metrics by marketing channel:</p>
- Site visits 
- Started applications 
- Submitted applications 
- Approved applications 
- Bound (purchased) policies 


As our pay structure varies by channel, e.g., cost per click, started app, bound policy, etc., we need to be able to forecast these metrics by channel to get an accurate CPA. For revenue it’s generally sufficient for the aggregated numbers across all marketing channels for the month to be accurate, so some error/variation for individual channels is acceptable if the aggregated numbers are accurate. However, the pay structure varies from channel to channel, as noted above, so there is still a need to try to minimize the error per channel to accurately predict costs.  The growth team had been generating this forecast in a very manual manner, using historical data and anticipated changes in marketing spend for the following month. The goal was to develop a more sophisticated model that can be automated. 


