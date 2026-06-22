# Week 1: Signal Analysis - Z-Score Mean Reversion

This repository contains my Week 1 quantitative analysis project. The objective is to rigorously evaluate a custom statistical mean-reversion trading signal against a passive buy-and-hold benchmark using historical market data.

## Part 1: Baseline Metrics and Return Distribution
I acquired 10 years of daily price and volume data for Apple (AAPL) using yfinance. To establish a strict mathematical baseline, I computed the daily log returns and analyzed the distribution for non-normal characteristics, specifically noting the heavy tails where extreme market moves occur. 

I then calculated the core performance metrics for the raw asset:
- Mean Daily and Annualised Return
- Annualised Volatility
- Annualised Sharpe Ratio (Assuming a 0 risk-free rate)
- Maximum Drawdown over the sample period
- Best and Worst Single Trading Days

These figures represent the absolute baseline. If the active strategy cannot outperform this passive hold, it lacks genuine utility.

## Part 2: Signal Formulation and Testing
Prior to implementation, I defined the strategy using a formal, mechanism-driven hypothesis:

"When a stock's daily return violently deviates by more than +/-2.2 standard deviations from its 20-day average [X], I expect the stock [Y] to mean-revert [Z] over a maximum of the next 2 days or until the Z-score neutralizes below +/-0.1 [N], because moves of this statistical magnitude represent temporary, unsustainable overreactions that quickly correct themselves as normal market liquidity returns [M]."

I translated this hypothesis into a programmatic signal. The logic calculates a 20-day rolling Z-score of daily returns. It triggers a Long state when the Z-score drops below -2.2, a Short state when it exceeds +2.2, and flattens the position after 2 days or when the deviation neutralizes. Following the simulation, I calculated the strategy's forward returns and compared its core metrics side-by-side with the Part 1 benchmark.

## Part 3: Statistical Validity and Risk Assessment
To avoid curve-fitting and false confidence, I conducted a critical evaluation of the backtest results:

- __The Primary Live Trading Risk:__ The greatest threat to this signal in live deployment is execution slippage at liquidity boundaries. This strategy explicitly triggers during moments of peak panic or euphoria. During these chaotic extremes, market makers widen bid-ask spreads significantly. The
