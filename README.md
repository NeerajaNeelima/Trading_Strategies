# Trading_Strategies

# My Notebook

This repository contains a Jupyter Notebook that can be run directly in **Google Colab**.

---

## How to Run

You can run this notebook in Google Colab without installing anything locally. Follow these steps:

1. **Open in Google Colab**  
   Click the **Open in Colab** badge below to open the notebook directly:

   [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://github.com/NeerajaNeelima/Trading_Strategies/blob/main/Trading_Strategies.ipynb)
   
2. **Run the Notebook**  
   - Click **Runtime → Run all** to execute all cells sequentially.  
   - Or run individual cells using the **play button** on the left of each cell.

3. **Install Dependencies (if required)**  
   If the notebook uses packages not pre-installed in Colab, add a cell at the top:

   ```python

   !pip install pandas matplotlib scikit-learn

   ```

### Methodology

#### Data Preparation
1.  **Data Overview**: Loaded two datasets, `fear_greed_index.csv` (Bitcoin Market Sentiment) and `historical_data.csv` (Historical Trader Data). Performed initial checks for rows, columns, and column names.
2.  **Missing Values & Duplicates**: Verified that both datasets had no missing values or duplicate entries.

#### Data Cleaning Steps
1.  **Timestamp Conversion**: Converted `timestamp` (in `fear` dataframe) and `Timestamp IST` (in `trades` dataframe) columns to a consistent date format (`YYYY-MM-DD`) to enable merging.
2.  **Daily Aggregation**: Grouped raw trader data by date to analyze daily trading behavior.
3.  **Data Merging**: Merged the `trades` dataset with the `fear` dataset on the `date` column, incorporating market sentiment `classification` into the trader data.

#### Key Metrics Created
After data cleaning and merging, daily trading metrics were calculated and grouped by `date` and `classification` (market sentiment):
-   **Daily PnL**: Sum of `Closed PnL` for each day.
-   **Trades per Day**: Count of trades per day.
-   **Wins**: Count of trades with positive `Closed PnL`.
-   **Average Trade Size**: Mean of `Size USD`.
-   **Win Rate**: Calculated as `Wins / Trades`.

#### Sentiment-Based Analysis
Daily metrics were then further grouped by `classification` (market sentiment) to understand trading performance under different market conditions. The following aggregated metrics were calculated:
-   **Average PnL**: Mean daily PnL for each sentiment.
-   **Average Win Rate**: Mean win rate for each sentiment.
-   **Average Trades**: Average number of trades per day for each sentiment.
-   **Average Trade Size**: Average trade size in USD for each sentiment.

#### Trader Segmentation Analysis
Individual trader performance was analyzed by grouping trades by `Account`. This segmentation aimed to identify profitable, active, and large-position traders.

**Metrics Calculated per Trader:**
-   **Total PnL**: Sum of `Closed PnL` for each trader.
-   **Total Trades**: Count of trades executed per trader.
-   **Average Trade Size**: Mean of `Size USD` for each trader.

**Trader Segmentation:**
Traders were categorized into segments based on the following criteria:
-   **Frequency**:
    -   `High Trader`: if `Trades` > 100
    -   `Low Trader`: if `Trades` <= 100
-   **Profitability**:
    -   `Winner`: if `Total PnL` > 0
    -   `Loser`: if `Total PnL` <= 0
-   **Size Group**:
    -   `Large Trader`: if `Avg Size` > 1000
    -   `Small Trader`: if `Avg Size` <= 1000

**Predictive Modeling (next_day_profit):**
Further analysis involved creating a `trader_daily` dataframe to predict `next_day_profit`.
-   `trader_daily`: Aggregated daily stats per trader (daily_pnl, trades, wins, avg_size, win_rate).
-   `next_day_pnl`: Shifted `daily_pnl` to represent the profit/loss of the subsequent day.
-   `next_day_profit`: Binary classification (1 if `next_day_pnl` > 0, else 0).
-   `classification_code`: Market sentiment (`classification`) was encoded numerically.
-   A Logistic Regression model was trained using `classification_code`, `trades`, `win_rate`, and `avg_size` to predict `next_day_profit`.

**KMeans Clustering for Trader Behavior:**
KMeans clustering was applied to `trader_behavior` (aggregated trader metrics: total_pnl, avg_trades, avg_size, win_rate) to identify distinct trader segments. Features were scaled using `StandardScaler` before clustering into 3 groups.

### Insights

**Key Insight 1**  
Traders trade more during **Fear**.  
- **Extreme Fear:** ~1528 trades/day  
- **Greed:** ~260 trades/day  

> Suggests panic or opportunity trading during fear.

**Key Insight 2**  
Win rate is higher during **Greed**.  
- **Extreme Greed win rate:** ~46%  
- **Fear win rate:** ~33%  

> Markets appear more predictable during Greed.

**Key Insight 3**  
PnL is higher during **Fear** despite lower win rate.  

**Possible reasons:**
- Bigger price moves  
- Higher volatility  
- Larger winning trades  


### Actionable Strategies

**Strategy 1 — Fear Strategy**
-   **Rule**: During Fear → Reduce trade frequency, Increase position quality
-   **Reason**: Fear has low win rate → Overtrading happens

**Strategy 2 — Greed Strategy**
-   **Rule**: During Greed → Increase trade frequency, Use trend-following
-   **Reason**: Higher win rate → More predictable moves

**Strategy 3 — Risk Strategy**
-   **Rule**: Extreme Fear → Lower leverage; Extreme Greed → Normal leverage
-   **Reason**: Fear has high volatility → Risk is higher
