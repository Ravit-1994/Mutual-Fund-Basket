# Mutual-Fund-Basket

The Analysis is aimed to find trends of NIFTY50 stocks for the past month and then create a Mutual Fund Basket containing stocks that provide optimized returns with prediction values for an investment horizon of 3, 5 and 10 years.


### EDA on the dataset:
Pre-processing, cleaning of dataset using Python and libraries was the first step.
Later, trends of these stocks for the time frame was found to understand the stock prices:
```python
plt.figure(figsize=(15,10), dpi =150)
plt.plot(funds_df['Date'], funds_df[funds_df.columns[1:]])

plt.legend(funds_df.columns[1:], loc ='right', bbox_to_anchor=(1.25, 0.5));
```
![download (3)](https://github.com/user-attachments/assets/89fb1944-a4e5-4931-85ed-e279b37260ec)

For the selection of stocks, created two KPIs: **Volatility** and **ROI(Return on Investment)**
```python

companies = funds_df.columns[1:]
volatility = funds_df[companies].std()
volatility = volatility.sort_values(ascending=False)


roi_companies = (funds_df[companies].iloc[-1] - funds_df[companies].iloc[0])/funds_df[companies].iloc[0] * 100
roi_companies = roi_companies.sort_values(ascending=False)
```

## For a Good Performing Mutual Fund, we would need a high ROI with low volatility stocks
```python
# A High ROI can be considered as being higher than the avg. returns of all the stocks for the time period
# This implies we select stocks --> Higher than Average ROI

roi_threshold = roi_companies.median()
volatile_threshold = volatility.median()

good_companies = roi_companies[(roi_companies > roi_threshold) & (volatility < volatile_threshold)]
good_companies = good_companies.sort_values(ascending=False)
good_companies
ICICIBANK.NS     13.480860
INDUSINDBK.NS     7.159914
JSWSTEEL.NS       7.021748
AXISBANK.NS       6.592466
HDFCBANK.NS       6.319839
SUNPHARMA.NS      5.627425
KOTAKBANK.NS      5.474481
CIPLA.NS          4.850117
NTPC.NS           4.356926
dtype: float64

# We get the stocks to be included in the fund but the percentage of investment for each stock i.e., weighted investment has to be adjusted

# To do this, we create a inverse volatility ratio based on which the percent of investment in each stock is calculated.

volatile_ratio = volatility[good_companies.index]
inverse_volatility_ratio = 1/volatile_ratio

# Get the investment weights by creating the weighted average
investment_percent = inverse_volatility_ratio/inverse_volatility_ratio.sum()
```


## Comparison of Mutual Fund created v/s the total Stocks
```python
# Average growth for the companies
average_growth_companies = (funds_df[companies].pct_change() * 100).mean()

# As we have 10 companies with high ROI and low volatility we will compare the results with the Top 10 companies from the original dataset.

top_companies = average_growth_companies.sort_values(ascending=False).head(10)
roi_mutual_fund = roi_companies[good_companies.index]
roi_top_companies = roi_companies[top_companies.index]

import plotly.graph_objs as go
import plotly.express as px

fig = go.Figure()

fig.add_trace(go.Bar(
    y=roi_mutual_fund.index,
    x=roi_mutual_fund,
    orientation='h',  
    name='Mutual Fund Companies',
    marker=dict(color='blue')
))

fig.add_trace(go.Bar(
    y=roi_top_companies.index,
    x=roi_top_companies,
    orientation='h',  
    name='Growth Rate Companies',
    marker=dict(color='green'),
    opacity=0.7
))

fig.update_layout(
    title='Expected ROI Comparison: Mutual Fund vs Growth Rate Companies',
    xaxis_title='Expected ROI (%)',
    yaxis_title='Companies',
    barmode='overlay',  
    legend=dict(title='Company Type'),
    template='plotly_white'
)

fig.show()
```
![newplot (1)](https://github.com/user-attachments/assets/76148934-eb38-44cc-86e9-bcbc6b0237bc)


```python
# Similary we will consider the volatility or Risk on this fund
risk_mutual_fund = volatility[good_companies.index]
risk_top_companies = volatility[top_companies.index]
fig = go.Figure()

fig.add_trace(go.Bar(
    y=risk_mutual_fund.index,
    x=risk_mutual_fund,
    orientation='h',  # Horizontal bar
    name='Mutual Fund Companies',
    marker=dict(color='blue')
))

fig.add_trace(go.Bar(
    y=risk_top_companies.index,
    x=risk_top_companies,
    orientation='h',  
    name='Growth Rate Companies',
    marker=dict(color='green'),
    opacity=0.7
))

fig.update_layout(
    title='Risk Comparison: Mutual Fund vs Growth Rate Companies',
    xaxis_title='Volatility (Standard Deviation)',
    yaxis_title='Companies',
    barmode='overlay',  
    legend=dict(title='Company Type'),
    template='plotly_white'
)

fig.show()
```
![newplot (2)](https://github.com/user-attachments/assets/9a283cab-ccd4-4aca-b16c-22c3191a296a)

## Calculating Expected Returns
To calculate the expected value a person will accumulate over 1 year, 3 years, 5 years, and 10 years through the mutual fund plan, we can follow these steps:

1. Assume the person is investing 5000 rupees every month.
1. Use the expected ROI from the mutual fund companies to simulate the growth over time.
1. Compute the compounded value of the investments for each period (1y, 3y, 5y, and 10y).
1. Visualize the accumulated value over these periods.
   
```python
monthly_investment = 5000  # Monthly investment in INR
years = [1, 3, 5, 10]  # Investment periods (in years)
n = 12  # Number of times interest is compounded per year (monthly)

avg_roi = roi_mutual_fund.mean() / 100  # Convert to decimal

def future_value(P, r, n, t):
    return P * (((1 + r/n)**(n*t) - 1) / (r/n)) * (1 + r/n)

future_values = [future_value(monthly_investment, avg_roi, n, t) for t in years]

fig = go.Figure()

fig.add_trace(go.Scatter(
    x=[str(year) + " year" for year in years],
    y=future_values,
    mode='lines+markers',
    line=dict(color='blue'),
    marker=dict(size=8),
    name='Future Value'
))

fig.update_layout(
    title="Expected Value of Investments of ₹ 5000 Per Month (Mutual Funds)",
    xaxis_title="Investment Period",
    yaxis_title="Future Value (INR)",
    xaxis=dict(showgrid=True, gridcolor='lightgrey'),
    yaxis=dict(showgrid=True, gridcolor='lightgrey'),
    template="plotly_white",
    hovermode='x'
)

fig.show()
```
![newplot (3)](https://github.com/user-attachments/assets/d93dde7e-ffb0-4d75-b44d-150ce27a4707)

