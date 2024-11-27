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

