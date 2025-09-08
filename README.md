# Portfolio
Quantitative Backtesting Model
 
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf

# 1. Download Data (example: DBS Bank on SGX)
ticker = "D05.SI"  # DBS Group Holdings on SGX
data = yf.download(ticker, start="2020-01-01", end="2025-01-01")

# 2. Strategy: Moving Average Crossover
data["SMA20"] = data["Close"].rolling(window=20).mean()
data["SMA50"] = data["Close"].rolling(window=50).mean()

# Trading signals
data["Signal"] = 0
data.loc[data["SMA20"] > data["SMA50"], "Signal"] = 1  # Long
data["Position"] = data["Signal"].shift(1)  # Execute at next day

# 3. Portfolio returns
data["Return"] = data["Close"].pct_change()
data["Strategy"] = data["Position"] * data["Return"]

# 4. Performance Metrics
cumulative_return = (1 + data["Strategy"]).cumprod() - 1
sharpe_ratio = (data["Strategy"].mean() / data["Strategy"].std()) * np.sqrt(252)
max_drawdown = (cumulative_return.cummax() - cumulative_return).max()

print("Cumulative Return:", cumulative_return.iloc[-1])
print("Sharpe Ratio:", sharpe_ratio)
print("Max Drawdown:", max_drawdown)

# 5. Plot results
plt.figure(figsize=(12,6))
plt.plot((1 + data["Return"]).cumprod(), label="Buy & Hold")
plt.plot((1 + data["Strategy"]).cumprod(), label="Strategy")
plt.title(f"Backtest of Moving Average Crossover on {ticker}")
plt.legend()
plt.show()

<img width="1142" height="828" alt="Screenshot 2025-09-04 at 22 00 50" src="https://github.com/user-attachments/assets/05ea2f9d-f337-4cd2-9be0-bca9ea33dcb6" />
