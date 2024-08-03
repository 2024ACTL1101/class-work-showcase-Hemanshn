
## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```r
# Load data from CSV file
amd_df <- read.csv("AMD.csv")
# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)
amd_df <- amd_df[, c("date", "close")]
```

#### Plotting the Data
Plot the closing prices over time to visualize the price movement.
```r
plot(amd_df$date, amd_df$close,'l')
```

### Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```r
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA  # Corrected column name
amd_df$accumulated_shares <- 0  # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

for (i in 1:nrow(amd_df)) {
if (previous_price == 0) { # First day, we buy 100 shares amd_df$trade_type[i] <- 'buy' amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size accumulated_shares <- accumulated_shares + share_size } else if (amd_df$close[i] < previous_price) { # Price dropped, we buy 100 shares amd_df$trade_type[i] <- 'buy' amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size accumulated_shares <- accumulated_shares + share_size }
if (i == nrow(amd_df)) { # Last day, we sell all accumulated shares amd_df$trade_type[i] <- 'sell' amd_df$costs_proceeds[i] <- amd_df$close[i] * accumulated_shares accumulated_shares <- 0 } amd_df$accumulated_shares[i] <- accumulated_shares previous_price <- amd_df$close[i]
}
```


### Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```r
# Filter the dataframe for the specified trading period start_date <- as.Date("2022-01-01") end_date <- as.Date("2023-01-01") trading_period <- amd_df[amd_df$date >= start_date & amd_df$date <= end_date,
```


### Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```r
# Initialize accumulated_shares accumulated_shares <- 0
Initialize variables for trading logic
Previous_price <- 0
share_size <- 100
Loop through the trading_period dataframe to execute trades
for (i in 1:nrow(trading_period)) {
if (previous_price == 0) {
# First day, we buy 100 shares
trading_period$trade_type[i] <- 'buy'
trading_period$costs_proceeds[i] <- -trading_period$close[i] * share_size
accumulated_shares <- accumulated_shares + share_size
} else if (trading_period$close[i] < previous_price)
# Price dropped, we buy 100 shares
trading_period$trade_type[i] <- 'buy'
trading_period$costs_proceeds[i] <- -trading_period$close[i] * share_size
accumulated_shares <- accumulated_shares + share_size
}
if (i == nrow(trading_period)) {
# Last day, we sell all accumulated shares
trading_period$trade_type[i] <- 'sell'
trading_period$costs_proceeds[i] <- trading_period$close[i] * accumulated_shares
accumulated_shares <- 0
}
trading_period$accumulated_shares[i] <- accumulated_shares
previous_price <- trading_period$close[i]
}
Calculate total profit/loss
total_profit_loss <- sum(trading_period$costs_proceeds, na.rm = TRUE)
Calculate total capital invested (negative sum of ‘costs_proceeds’ for ‘buy’ transactions)
total_invested_capital <- -sum(trading_period$costs_proceeds[trading_period$trade_type == 'buy'], na.rm = TRUE)
Calculate ROI
if (total_invested_capital != 0) {
ROI <- (total_profit_loss / abs(total_invested_capital)) * 100 # Using absolute value for clarity
} else {
ROI <- 0 # Handle division by zero case if total_invested_capital is zero
}
Print the results for profit/loss, total capital invested and ROI cat("Total Profit/Loss:", total_profit_loss, "\n") cat("Total Capital Invested:", total_invested_capital, "\n") cat("ROI:", ROI, "%\n")
```

### Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```r
# Reinitialize the trading_period data frame and variables trading_period$trade_type <- NA trading_period$costs_proceeds <- NA trading_period$accumulated_shares <- 0 previous_price <- 0 share_size <- 100 accumulated_shares <- 0 total_costs <- 0 # Define the profit-taking threshold to which half the shares will be sold once the threshold is met profit_threshold <- 1.05 for (i in 1:nrow(trading_period)) { if (previous_price == 0) { # First day, we buy 100 shares trading_period$trade_type[i] <- 'buy' trading_period$costs_proceeds[i] <- -trading_period$close[i] * share_size accumulated_shares <- accumulated_shares + share_size total_costs <- total_costs + trading_period$costs_proceeds[i] } else if (trading_period$close[i] < previous_price) { # Price dropped, we buy 100 shares trading_period$trade_type[i] <- 'buy' trading_period$costs_proceeds[i] <- -trading_period$close[i] * share_size accumulated_shares <- accumulated_shares + share_size total_costs <- total_costs + trading_period$costs_proceeds[i] } else if (accumulated_shares > 0) { # Calculate average purchase price average_purchase_price <- -total_costs / accumulated_shares if (trading_period$close[i] > average_purchase_price * profit_threshold) { # Price has increased by the profit threshold, we sell half of the holdings shares_to_sell <- accumulated_shares / 2 trading_period$trade_type[i] <- 'sell' trading_period$costs_proceeds[i] <- trading_period$close[i] * shares_to_sell accumulated_shares <- accumulated_shares - shares_to_sell total_costs <- total_costs + trading_period$costs_proceeds[i] } }
if (i == nrow(trading_period)) { # Last day, we sell all accumulated shares trading_period$trade_type[i] <- 'sell' trading_period$costs_proceeds[i] <- trading_period$close[i] * accumulated_shares accumulated_shares <- 0 } trading_period$accumulated_shares[i] <- accumulated_shares previous_price <- trading_period$close[i] } # Recalculate total profit/loss and ROI after implementing the strategy total_profit_loss <- sum(trading_period$costs_proceeds, na.rm = TRUE) total_invested_capital <- -sum(trading_period$costs_proceeds[trading_period$trade_type == 'buy'], na.rm = TRUE) if (total_invested_capital != 0) { ROI <- (total_profit_loss / abs(total_invested_capital)) * 100 } else { ROI <- 0 } cat("Total Profit/Loss after profit-taking strategy:", total_profit_loss, "\n") cat("Total Capital Invested after profit-taking strategy:", total_invested_capital, "\n") cat("ROI after profit-taking strategy:", ROI, "%\n")
```


### Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```r
In January of 2022, reports had suggested AMD’s supply chain management issue due to a global semi-conductor shortage. AMD had a decrease in production by almost 8.9% from the fiscal year ending in December 2021 to December 2022. This would have instigated a sense of uncertainty within investors and hence reflected in the fall of AMD’s stock price over the financial year in 2022 as seen in the graph in step 1. Furthermore, AMD’s 2 largest competitors, NVIDIA and Intel shared reports of increasing production alongside plans for newer updated chips which would have ignited new interest from investors. Specifically, NVIDIA had a dominant year with its Ampere architecture which posed threats to AMD’s Radeon lineup, while Intel had announced an improved plan for its new semi-conductor productions. Hence, after strategy 1 was implemented in step 4, it resulted in a loss of -335456 which translated to an ROI of -27.43332 %. After implementing the profit taking strategy, there was an improvement in both the profit and ROI as the total profit summed to 26143.24 and the ROI increased to 2.137973 %. This would’ve occurred as I implemented a profit taking strategy with a threshold of 5%, and since the stock price had occasional booms through out 2022, as seen in the plot of the data where between 2022 and 2023 there was an overall decline in the stock however 5-6 major booms occurred along the way as well, the 5% profit accumulated by the shares once they were sold, would have counteracted the shortcomings in the first strategy.
```

Sample Discussion: On Wednesday, December 6, 2023, AMD CEO Lisa Su discussed a new graphics processor designed for AI servers, with Microsoft and Meta as committed users. The rise in AMD shares on the following Thursday suggests that investors believe in the chipmaker's upward potential and market expectations; My first strategy earned X dollars more than second strategy on this day, therefore providing a better ROI.




