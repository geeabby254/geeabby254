# Simple Moving Average Crossover Strategy
data['SMA50'] = data['Close'].rolling(window=50).mean()
data['SMA200'] = data['Close'].rolling(window=200).mean()
data['Signal'] = 0
data['Signal'][50:] = np.where(data['SMA50'][50:] > data['SMA200'][50:], 1, 0)
data['Position'] = data['Signal'].diff()

# Backtesting
initial_capital = 100000
positions = pd.DataFrame(index=data.index).fillna(0.0)
positions['AAPL'] = data['Signal']
portfolio = positions.multiply(data['Close'], axis=0)
pos_diff = positions.diff()

portfolio['Holdings'] = (positions.multiply(data['Close'], axis=0)).sum(axis=1)
portfolio['Cash'] = initial_capital - (pos_diff.multiply(data['Close'], axis=0)).sum(axis=1).cumsum()
portfolio['Total'] = portfolio['Cash'] + portfolio['Holdings']
portfolio['Returns'] = portfolio['Total'].pct_change()

# Plotting the results
import matplotlib.pyplot as plt

plt.figure(figsize=(12,8))
plt.plot(portfolio['Total'], label='Portfolio Value')
plt.title('Portfolio Value Over Time')
plt.xlabel('Date')
plt.ylabel('Portfolio Value')
plt.legend()
plt.show()
