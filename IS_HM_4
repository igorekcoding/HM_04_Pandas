# Import data
import os
import requests
import pandas as pd
import alpaca_trade_api as tradeapi
from MCForecastTools import MCSimulation
from matplotlib import pyplot as plt
import warnings

warnings.simplefilter(action='ignore', category=FutureWarning)

my_btc = 1.2
my_eth = 5.3

btc_url = "https://api.alternative.me/v2/ticker/Bitcoin/?convert=USD"
eth_url = "https://api.alternative.me/v2/ticker/Ethereum/?convert=USD"

# Fetch current BTC price
btc_response_data = requests.get(btc_url)

# Fetch current ETH price
eth_response_data = requests.get(eth_url)

# Compute current value of my crpto
import json
btc_data = btc_response_data.json()
my_btc_value = my_btc * btc_data['data']['1']['quotes']['USD']['price']

eth_data = eth_response_data.json()
my_eth_value = my_eth * eth_data['data']['1027']['quotes']['USD']['price']

# Print current crypto wallet balance
print(f"The current value of your {my_btc} BTC is ${my_btc_value:0.2f}")
print(f"The current value of your {my_eth} ETH is ${my_eth_value:0.2f}")

# Current amount of shares
my_agg = 200
my_spy = 50

# Set Alpaca API key and secret
alpaca_api_key = 'PKGKN62IDHI8XUKZRSRT'
alpaca_secret_key = 'kSYcC14LAEPd2KZZK5DJ4vUNksrGMNhRjWeCy4DD'

# Create the Alpaca API object
api = tradeapi.REST(
    alpaca_api_key,
    alpaca_secret_key,
    api_version = "v2"
)

# Format current date as ISO format
start_date = pd.Timestamp("2021-01-12", tz="America/New_York").isoformat()

# Set the tickers
tickers = ["AGG", "SPY"]

# Set timeframe to '1D' for Alpaca API
timeframe = "1D"

# Get current closing prices for SPY and AGG
ticker_data = api.get_barset(
    tickers,
    timeframe,
    start=start_date
).df

# Preview DataFrame
print(ticker_data.head())


# Pick AGG and SPY close prices
agg_close_price = ticker_data['AGG']['close']['2021-01-12']
spy_close_price = ticker_data['SPY']['close']['2021-01-12']

# Print AGG and SPY close prices
print(f"Current AGG closing price: ${agg_close_price}")
print(f"Current SPY closing price: ${spy_close_price}")

# Compute the current value of shares
my_agg_value = my_agg * agg_close_price
my_spy_value = my_spy * spy_close_price

# Print current value of share
print(f"The current value of your {my_spy} SPY shares is ${my_spy_value:0.2f}")
print(f"The current value of your {my_agg} AGG shares is ${my_agg_value:0.2f}")

# Set monthly household income
monthly_income = 12000

# Create savings DataFrame
crypto_value = my_btc_value + my_eth_value
shares_value = my_spy_value + my_agg_value
df = {'amount' : {
        "crypto" : crypto_value,
        "shares" : shares_value
        }
    }
df_savings = pd.DataFrame(df)

# Display savings DataFrame
print(df_savings)

# Plot savings pie chart
df_savings.plot.pie(y='amount', title='Composition of Personal Savings')
plt.show()

# Set ideal emergency fund
emergency_fund = monthly_income * 3

# Calculate total amount of savings
total_savings = df['amount']['crypto'] + df['amount']['shares']

# Validate saving health
if total_savings > emergency_fund:
    print("Congratulations! You have enough money in your emergency fund.")
elif total_savings == emergency_fund:
    print("Congratulations! You have reached your financial goal.")
else:
    amount_to_goal = emergency_fund - total_savings
    print(f"You are ${amount_to_goal:.2f} away from your goal.  You're almost there. Keep going!")


#! Part 2 - Retirement Planning
#! Monte Carlo Simulation

# Set start and end dates of five years back from today.
# Sample results may vary from the solution based on the time frame chosen
start_date = pd.Timestamp('2016-01-12', tz='America/New_York').isoformat()
end_date = pd.Timestamp('2021-01-12', tz='America/New_York').isoformat()

# Get 5 years' worth of historical data for SPY and AGG
df_stock_data = api.get_barset(
    tickers,
    timeframe,
    start=start_date,
    end=end_date
).df

# Display sample data
print(df_stock_data.head())

# Configuring a Monte Carlo simulation to forecast 30 years cumulative returns
MC_even_dist = MCSimulation(
    portfolio_data = df_stock_data,
    weights = [.4,.6],
    num_simulation = 500,
    num_trading_days = 252 * 30
)

# Printing the simulation input data
print(MC_even_dist.portfolio_data.head())

# Running a Monte Carlo simulation to forecast 30 years cumulative returns
print(MC_even_dist.calc_cumulative_return())

# Plot simulation outcomes
line_plot = MC_even_dist.plot_simulation()
print(line_plot)
plt.show()

# Plot probability distribution and confidence intervals
dist_plot = MC_even_dist.plot_distribution()
print(dist_plot)
plt.show()

# Fetch summary statistics from the Monte Carlo simulation results
even_tbl = MC_even_dist.summarize_cumulative_return()

#! Retirement Analysis

# Print summary statistics
print(even_tbl)

#! Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a $20,000 initial investment.

# Set initial investment
initial_investment = 20000

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $20,000
ci_lower = round(even_tbl[8]*initial_investment,2)
ci_upper = round(even_tbl[9]*initial_investment,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")

#! Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a 50% increase in the initial investment.

# Set initial investment
initial_investment = 20000 * 1.5

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $30,000
ci_lower = round(even_tbl[8]*initial_investment,2)
ci_upper = round(even_tbl[9]*initial_investment,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")


#? Optional Challenge - Early Retirement

#! Five Years Retirement Option

# Configuring a Monte Carlo simulation to forecast 30 years cumulative returns
MC_even_dist = MCSimulation(
    portfolio_data = df_stock_data,
    weights = [.4,.6],
    num_simulation = 500,
    num_trading_days = 252 * 5
)

# Printing the simulation input data
print(MC_even_dist.portfolio_data.head())

# Running a Monte Carlo simulation to forecast 30 years cumulative returns
print(MC_even_dist.calc_cumulative_return())

# Plot simulation outcomes
line_plot = MC_even_dist.plot_simulation()
print(line_plot)
plt.show()

# Plot probability distribution and confidence intervals
dist_plot = MC_even_dist.plot_distribution()
print(dist_plot)
plt.show()

# Fetch summary statistics from the Monte Carlo simulation results
even_tbl = MC_even_dist.summarize_cumulative_return()

#! Retirement Analysis

# Print summary statistics
print(even_tbl)

#! Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a $20,000 initial investment.

# Set initial investment
initial_investment = 20000

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $20,000
ci_lower = round(even_tbl[8]*initial_investment,2)
ci_upper = round(even_tbl[9]*initial_investment,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")


#! Ten Years Retirement Option

# Configuring a Monte Carlo simulation to forecast 30 years cumulative returns
MC_even_dist = MCSimulation(
    portfolio_data = df_stock_data,
    weights = [.4,.6],
    num_simulation = 500,
    num_trading_days = 252 * 10
)

# Printing the simulation input data
print(MC_even_dist.portfolio_data.head())

# Running a Monte Carlo simulation to forecast 30 years cumulative returns
print(MC_even_dist.calc_cumulative_return())

# Plot simulation outcomes
line_plot = MC_even_dist.plot_simulation()
print(line_plot)
plt.show()

# Plot probability distribution and confidence intervals
dist_plot = MC_even_dist.plot_distribution()
print(dist_plot)
plt.show()

# Fetch summary statistics from the Monte Carlo simulation results
even_tbl = MC_even_dist.summarize_cumulative_return()

#! Retirement Analysis

# Print summary statistics
print(even_tbl)

#! Calculate the expected portfolio return at the 95% lower and upper confidence intervals based on a $20,000 initial investment.

# Set initial investment
initial_investment = 20000

# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes of our $20,000
ci_lower = round(even_tbl[8]*initial_investment,2)
ci_upper = round(even_tbl[9]*initial_investment,2)

# Print results
print(f"There is a 95% chance that an initial investment of ${initial_investment} in the portfolio"
      f" over the next 30 years will end within in the range of"
      f" ${ci_lower} and ${ci_upper}")