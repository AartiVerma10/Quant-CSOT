# Quantitative Trading Strategies Toolkit

This repository contains a collection of nine algorithmic trading strategies based strictly on market microstructure (price and volume). It is designed to test behavioral finance hypotheses using end-of-day trading data.

## Table of Contents

2. [Core Performance Metrics](#core-performance-metrics)
3. [Data Requirements](#data-requirements)
4. [Bullish Strategies (Going Long)](#bullish-strategies-going-long)
5. [Bearish Strategies (Short Selling)](#bearish-strategies-short-selling)
6. [Quick Reference Table](#quick-reference-table)
7. [Implementation Notes](#implementation-notes)


---

## Core Performance Metrics

Every strategy must be evaluated against a standard "buy-and-hold" benchmark using these five quantitative vital signs:

* **Mean Daily Return:** The simple average of daily percentage changes.
* **Annualised Return:** The compounded annual growth rate: `(1 + R_daily)^252 - 1`
* **Annualised Volatility:** The risk or "bounciness" of the asset: `Daily_Standard_Deviation * sqrt(252)`
* **Annualised Sharpe Ratio:** The risk-adjusted return (assuming a risk-free rate of 0): `Annualised Return / Annualised Volatility`
* **Maximum Drawdown (MDD):** The worst-case peak-to-trough drop, calculated using a cumulative equity curve and a high-water mark (`.cummax()`).

---

## Data Requirements

These strategies rely strictly on historical End-of-Day (EOD) data. No fundamental or alternative data is required.
* `Open`, `High`, `Low`, `Close` (OHLC)
* `Volume`

---
### 1. The Panic Absorption (Mean Reversion / Long)

The Entry Trigger: You do not buy while the stock is crashing in the morning. You wait until 3:45 PM (right before the market closes). If the volume is massive and the price has formed a long lower wick (meaning it bounced back up), you buy at the close.

The Stop-Loss: Placed exactly 1 cent below the absolute low of that day's panic candle.

Why? Because your hypothesis was that institutions bought up all the supply at that low price. If the price drops below that low tomorrow, your hypothesis is wrong—the institutions aren't defending it, and the crash is continuing. Get out immediately.

The Target: Sell when the price bounces back to its 10-day moving average, or at the high of the previous day before the crash started.


### 2. The Volatility Squeeze Breakout (Momentum / Long)
This strategy is about catching an explosive move the second it starts.

The Entry Trigger: Unlike the Panic Absorption, you do not wait for the end of the day. You place a "Buy Stop" order just above the 10-day high. The exact second the price crosses that line on high volume, you are in the trade.

The Stop-Loss: Placed just below the low of the 4-day tight range.

Why? A true momentum breakout should never fall back into its consolidation range. If it drops back into that tight 4-day box, it is a "false breakout" (a trap).

The Target: Momentum trades can run for days or weeks. Use a Trailing Stop-Loss. Once you are in profit, move your stop-loss up to the low of the previous day. Keep doing this every single day until you are eventually stopped out.

### 3. The Buying Exhaustion Climax (Fading the Top / Short)
This is a highly aggressive strategy where you bet against a stock that has gone completely parabolic.

The Entry Trigger: Wait until the final 30 minutes of the trading day. If the stock gapped up in the morning but is now bleeding out and closing near its low on record-breaking volume, you enter your short position.

The Stop-Loss: Placed exactly above the absolute high of that day's climax candle.

Why? Shorting parabolic stocks is incredibly dangerous. If the stock breaks above today's high tomorrow, it means the FOMO (Fear of Missing Out) is still going, and the stock could squeeze another 50%. You must cut your loss instantly.

The Target: Target the "Gap Fill." Look at the chart and find the price where the massive 4-day run originally started. That is where you buy back your shares to take profit.

### 4. The Inside Bar Squeeze (Consolidation / Long)
This strategy plays the release of built-up pressure.

The Entry Trigger: You buy the exact moment the price breaks above the high of the "Inside Bar" candle.

The Stop-Loss: Placed just below the low of the Inside Bar.

Why? The inside bar represents an equilibrium. If the price breaks the low instead of the high, the sellers have won the battle.

The Target: A fixed 1:2 or 1:3 Risk/Reward ratio. If your Stop-Loss is $1 below your entry price, you set a strict limit order to sell when the price goes $2 or $3 above your entry.

The Golden Rule: Risk vs. Reward

### 5. The Capitulation V-Bottom
A highly aggressive bottom-fishing strategy targeting the exact moment the last weak seller exits the market.
* **Hypothesis:** When a stock falls for 5 days, printing a massive red candle on 3x volume, followed by a green candle closing above the previous high, I expect a strong rebound.
* **Logic:** 5 red days AND Day 5 `Volume` > 3x 20-day Avg AND Day 6 `Close` > Day 5 `High`.
* **Execution:** Entry at MOC on Day 6. Stop-Loss below the absolute low of Day 5. Target the 20-day SMA.

### 6. The Silent Accumulation
A patient strategy tracking the subtle footprint of institutional funds building massive positions.
* **Hypothesis:** When a stock trades in a tight 5% range for 10 days, but average volume on up-days is >1.5x the volume on down-days, I expect an upward breakout.
* **Logic:** 10-day range < 5% AND Avg Green Day Volume > 1.5x Avg Red Day Volume.
* **Execution:** Entry via Buy Stop above the 10-day range. Stop-Loss below the 10-day range. Target a 1:3 Risk/Reward.

---

## Bearish Strategies (Short Selling)

### 7. The Retail Gap Trap
Capitalizes on retail traders overreacting to morning hype, only to be crushed by institutional profit-taking.
* **Hypothesis:** When a stock gaps up >3% at the open but closes below its open on higher-than-average volume, I expect the stock to decline.
* **Logic:** `Open` > (Prev Close * 1.03) AND `Close` < `Open` AND `Volume` > 20-day Avg.
* **Execution:** Short at MOC. Stop-Loss above the high of the gap. Target the previous day's close (Gap Fill).

### 8. The Buying Exhaustion Climax
Identifies a "blowoff top" where parabolic FOMO buying runs out of liquidity.
* **Hypothesis:** When a stock in a 4-day rally gaps to a 50-day high but closes in the bottom 25% of its range on the highest volume in 50 days, I expect a sharp decline.
* **Logic:** 4 green days AND `High` == 50-day high AND `Close` < `Low` + (0.25 * Range) AND `Volume` == 50-day Max.
* **Execution:** Short at MOC. Stop-Loss above the high of the climax candle. Target the origin of the parabolic run.

### 9. The Bull Trap Fakeout
Targets false breakouts engineered by institutions to trap retail buyers before driving the price down.
* **Hypothesis:** When a stock breaks a 50-day high but violently reverses to close below the previous day's close on 2x average volume, I expect the stock to decline.
* **Logic:** `High` > 50-day high AND `Close` < Prev `Close` AND `Volume` > 2x 20-day Avg.
* **Execution:** Short at MOC. Stop-Loss above today's new high. Target a 1:2 Risk/Reward.
---

## Bearish Strategies (Short Selling)

### 7. The Retail Gap Trap
Capitalizes on retail traders overreacting to morning hype, only to be crushed by institutional profit-taking.
> **Hypothesis:** When a stock gaps up >3% at the open but closes below its open on higher-than-average volume, I expect the stock to decline over the next 3 days.
* **Logic:** `Open` > (Prev Close * 1.03) AND `Close` < `Open` AND `Volume` > 20-day Avg.

### 8. The Buying Exhaustion Climax
Identifies a "blowoff top" where parabolic FOMO buying runs out of liquidity.
> **Hypothesis:** When a stock in a 4-day rally gaps to a 50-day high but closes in the bottom 25% of its range on the highest volume in 50 days, I expect a sharp decline over the next 5 days.
* **Logic:** 4 green days AND `High` == 50-day high AND `Close` < `Low` + (0.25 * Range) AND `Volume` == 50-day Max.

### 9. The Bull Trap Fakeout
Targets false breakouts engineered by institutions to trap retail buyers before driving the price down.
> **Hypothesis:** When a stock breaks a 50-day high but violently reverses to close below the previous day's close on 2x average volume, I expect the stock to decline over the next 3 to 5 days.
* **Logic:** `High` > 50-day high AND `Close` < Prev `Close` AND `Volume` > 2x 20-day Avg.

---

## Quick Reference Table

| Strategy Name | Direction | Core Mechanism | Key Price Action | Key Volume Action |
|---|---|---|---|---|
| **Panic Absorption** | Long | Mean Reversion | 20-day low, closes near top | 2x 30-day Avg |
| **Volatility Squeeze** | Long | Momentum | Breaks 10-day high after 4 flat days | 1.5x 20-day Avg |
| **Retail Gap Trap** | Short | Gap Reversal | Gaps up 3%, closes red | > 20-day Avg |
| **Institutional Pocket** | Long | Trend Continuation | Green bounce after 2-3 day pullback | 2x 20-day Avg |
| **Exhaustion Climax** | Short | Blowoff Top | 50-day high, closes at bottom | 50-day Max Volume |
| **Inside Bar Squeeze** | Long | Consolidation | Breaks above "inside bar" day | < 0.6x 20-day Avg |
| **Capitulation V-Bottom**| Long | Crash Reversal | Green engulfs a 5-day crash low | 3x 20-day Avg (on crash) |
| **Bull Trap Fakeout** | Short | False Breakout | Breaks 50-day high, closes red | 2x 20-day Avg |
| **Silent Accumulation** | Long | Pre-Breakout | Flat 5% range for 10 days | Green > 1.5x Red Avg |

---

## Implementation Notes
* **Signal Generation:** All conditional logic should be mapped to a binary `Signal` column (1 for active trade, 0 for cash).
* **Execution Shift:** Daily strategy returns must be calculated using `Signal.shift(1) * Asset_Return` to account for End-of-Day execution constraints (you cannot trade today's return using a signal generated at today's close). 



```python
import pandas as pd
import numpy as np

def generate_signals(df):
    """
    Takes a DataFrame with columns: ['Open', 'High', 'Low', 'Close', 'Volume']
    and adds signal columns for 9 quantitative strategies.
    Signal = 1 (Go Long), Signal = -1 (Go Short), Signal = 0 (Flat/Cash)
    """
    # Pre-calculate common rolling metrics to save time
    range_ = df['High'] - df['Low']
    vol_20_avg = df['Volume'].rolling(20).mean()
    vol_30_avg = df['Volume'].rolling(30).mean()
    
    # ---------------------------------------------------------
    # BULLISH STRATEGIES (LONG: Signal = 1)
    # ---------------------------------------------------------
    
    # 1. The Panic Absorption
    is_20d_low = df['Low'] == df['Low'].rolling(20).min()
    close_top_25 = df['Close'] > (df['Low'] + 0.75 * range_)
    vol_spike_30 = df['Volume'] > (2 * vol_30_avg)
    df['Sig_Panic_Absorption'] = (is_20d_low & close_top_25 & vol_spike_30).astype(int)

    # 2. The Volatility Squeeze Breakout
    avg_range_20 = range_.rolling(20).mean()
    tight_4_days = (range_ < avg_range_20).rolling(4).sum() == 4
    breakout = df['Close'] > df['High'].rolling(10).max().shift(1)
    vol_spike_20 = df['Volume'] > (1.5 * vol_20_avg)
    df['Sig_Vol_Squeeze'] = (tight_4_days.shift(1) & breakout & vol_spike_20).astype(int)

    # 3. The Institutional Pocket
    two_red_days = (df['Close'] < df['Open']).rolling(2).sum() == 2
    green_today = df['Close'] > df['Open']
    df['Sig_Inst_Pocket'] = (two_red_days.shift(1) & green_today & (df['Volume'] > 2 * vol_20_avg)).astype(int)

    # 4. The Inside Bar Squeeze
    inside_bar = (df['High'] < df['High'].shift(1)) & (df['Low'] > df['Low'].shift(1))
    low_vol = df['Volume'] < (0.6 * vol_20_avg)
    breakout_inside = df['Close'] > df['High'].shift(1)
    df['Sig_Inside_Bar'] = (inside_bar.shift(1) & low_vol.shift(1) & breakout_inside).astype(int)

    # 5. The Capitulation V-Bottom
    five_red_days = (df['Close'] < df['Open']).rolling(5).sum() == 5
    crash_vol = df['Volume'] > (3 * vol_20_avg)
    engulfing_green = df['Close'] > df['High'].shift(1)
    df['Sig_V_Bottom'] = (five_red_days.shift(1) & crash_vol.shift(1) & engulfing_green).astype(int)

    # 6. The Silent Accumulation
    ten_day_high = df['High'].rolling(10).max()
    ten_day_low = df['Low'].rolling(10).min()
    tight_range = ((ten_day_high - ten_day_low) / ten_day_low) < 0.05
    
    is_up = df['Close'] > df['Open']
    is_down = df['Close'] < df['Open']
    avg_up_vol = (df['Volume'] * is_up).rolling(10).sum() / is_up.rolling(10).sum().replace(0, 1)
    avg_down_vol = (df['Volume'] * is_down).rolling(10).sum() / is_down.rolling(10).sum().replace(0, 1)
    
    df['Sig_Silent_Acc'] = (tight_range & (avg_up_vol > 1.5 * avg_down_vol)).astype(int)

    # ---------------------------------------------------------
    # BEARISH STRATEGIES (SHORT: Signal = -1)
    # ---------------------------------------------------------

    # 7. The Retail Gap Trap
    gap_up_3pct = df['Open'] > (df['Close'].shift(1) * 1.03)
    closed_red = df['Close'] < df['Open']
    df['Sig_Gap_Trap'] = (gap_up_3pct & closed_red & (df['Volume'] > vol_20_avg)).astype(int) * -1

    # 8. The Buying Exhaustion Climax
    four_green_days = (df['Close'] > df['Open']).rolling(4).sum() == 4
    hit_50d_high = df['High'] == df['High'].rolling(50).max()
    close_bottom_25 = df['Close'] < (df['Low'] + 0.25 * range_)
    highest_vol_50 = df['Volume'] == df['Volume'].rolling(50).max()
    df['Sig_Exhaustion'] = (four_green_days.shift(1) & hit_50d_high & close_bottom_25 & highest_vol_50).astype(int) * -1

    # 9. The Bull Trap Fakeout
    broke_50d_high = df['High'] > df['High'].rolling(50).max().shift(1)
    closed_lower_than_yest = df['Close'] < df['Close'].shift(1)
    df['Sig_Bull_Trap'] = (broke_50d_high & closed_lower_than_yest & (df['Volume'] > 2 * vol_20_avg)).astype(int) * -1

    return df


def calculate_performance(df, signal_column):
    """
    Calculates the benchmark returns and the strategy returns, 
    then computes the 4 core metrics for comparison.
    """
    # Calculate daily asset returns
    df['Asset_Return'] = df['Close'].pct_change()
    
    # Calculate strategy return: Shift signal by 1 day to simulate buying at today's close 
    # and earning tomorrow's return.
    df['Strategy_Return'] = df[signal_column].shift(1) * df['Asset_Return']
    
    # Drop NaNs generated by rolling calculations
    df = df.dropna().copy()

    def get_metrics(returns_series):
        mean_daily = returns_series.mean()
        
        # Annualised Return
        ann_return = (1 + mean_daily)**252 - 1
        
        # Annualised Volatility
        ann_vol = returns_series.std() * np.sqrt(252)
        
        # Annualised Sharpe Ratio
        sharpe = ann_return / ann_vol if ann_vol != 0 else 0
        
        # Maximum Drawdown
        cum_equity = (1 + returns_series).cumprod()
        peak = cum_equity.cummax()
        drawdown = (cum_equity - peak) / peak
        max_dd = drawdown.min()
        
        return ann_return, ann_vol, sharpe, max_dd

    bench_ret, bench_vol, bench_sharpe, bench_mdd = get_metrics(df['Asset_Return'])
    strat_ret, strat_vol, strat_sharpe, strat_mdd = get_metrics(df['Strategy_Return'])

    print(f"--- Performance for {signal_column} ---")
    print("Metric                 | Benchmark (Buy & Hold) | Active Strategy")
    print("-----------------------|------------------------|----------------")
    print(f"Annualised Return      | {bench_ret*100:>14.2f}% | {strat_ret*100:>13.2f}%")
    print(f"Annualised Volatility  | {bench_vol*100:>14.2f}% | {strat_vol*100:>13.2f}%")
    print(f"Sharpe Ratio           | {bench_sharpe:>15.2f} | {strat_sharpe:>14.2f}")
    print(f"Maximum Drawdown       | {bench_mdd*100:>14.2f}% | {strat_mdd*100:>13.2f}%\n")


# ---------------------------------------------------------
# EXAMPLE USAGE:
# ---------------------------------------------------------
# df = pd.read_csv('your_stock_data.csv') 
# df = generate_signals(df)
# calculate_performance(df, 'Sig_Panic_Absorption')
# calculate_performance(df, 'Sig_Gap_Trap')
```