# Stock Market Analysis Dashboard
![image](https://github.com/user-attachments/assets/4d547fca-3d76-464c-9987-9eb4a7e9a620)



This dashboard visualizes stock market data, tracking key financial metrics such as opening price, high, low, and closing prices across various companies. The visualization includes dynamic filtering using slicers for company tickers and historical periods.

## Data Pipeline: yfinance → SQL Server → Power BI

### 🔄 Data Extraction with `yfinance`
We utilized the `yfinance` Python package to extract historical stock data, fetching key attributes like:
- **Open**: Opening price of the stock.
- **High**: Highest trading price of the day.
- **Low**: Lowest trading price of the day.
- **Close**: Closing price at market end.
- **Ticker Information**: AAPL, AMZN, MSFT, etc.
- **Date-Based Segmentation**: Year, Quarter, Month, and Day.

#### Python Script for Data Extraction:
```python
import yfinance as yf
import pandas as pd
import pyodbc

# Define ticker symbols
tickers = ['AAPL', 'AMZN', 'GOOG', 'MSFT', 'META', 'NVDA', 'SPY']

# Fetch historical data
data = yf.download(tickers, start='2014-01-01', end='2023-12-31')
data = data['Adj Close'].reset_index()

# Connect to SQL Server
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=your_server;DATABASE=your_db;UID=your_user;PWD=your_password')
cursor = conn.cursor()

# Insert data into SQL Server
for index, row in data.iterrows():
    cursor.execute("INSERT INTO stock_data (date, ticker, close_price) VALUES (?, ?, ?)", row['Date'], row['Ticker'], row['Adj Close'])

conn.commit()
cursor.close()
conn.close()
```

### 📡 SQL Server Connection & OAuth Integration in Power BI
- **Data Storage**: The extracted stock data is stored in SQL Server for efficient retrieval.
- **OAuth Authentication**: Power BI connects to SQL Server using OAuth, ensuring secure access without hardcoded credentials.
- **Dynamic Data Refresh**: Data is updated periodically, allowing real-time tracking of stock performance.

### 📊 Calculated Fields in Power BI
To enhance analytical insights, we created calculated fields such as:
- **Daily Percentage Change**:
  ```DAX
  Daily Change % = (Close - Open) / Open * 100
  ```
- **Moving Average (30-day)**:
  ```DAX
  Moving Avg 30 = AVERAGEX(DATESINPERIOD(stock_data[Date], LASTDATE(stock_data[Date]), -30, DAY), stock_data[Close])
  ```

### 🔍 Dynamic Filtering with Slicers
- **Ticker Slicer**: Enables selecting specific stocks to analyze.
- **Date Hierarchy**: Users can filter stock trends by Year, Quarter, Month, and Day.
- **Performance Metrics**: Allows users to dynamically view historical highs and lows.

## 🚀 Use Cases
- **Market Trend Analysis**: Track stock performance over time.
- **Investment Decision Support**: Identify stocks with consistent growth trends.
- **Historical Event Analysis**: Observe stock behavior around key financial events.

This end-to-end pipeline enables real-time financial analysis using Power BI, SQL Server, and Python, delivering actionable insights for investors and analysts.

