# Statistical Arbitrage/Market Inefficiency Exploitation

## Overview

This repository implements a market-neutral statistical arbitrage strategy exploiting pricing inefficiencies between:

- **Bitcoin spot rate (BTC/USD)**
- **Bitcoin futures**
- **Bitcoin ETF (IBIT)**

This is part of the final project for FINS3666 at UNSW: Trading and Market Making.

Although these instruments track the same underlying asset, they often diverge due to supply–demand imbalances, trading-hour mismatches, and institutional constraints. We leverage mean-reversion and co-integration analysis to profit from these temporary spreads.


## Background

Microstructure research reveals frequent volatility and transient dislocations. To avoid false signals, we apply conservative z-score thresholds. Our core assumption: price deviations between instruments revert to their historical relationship, enabling controlled arbitrage.

## Strategy

1. **Spread Calculation**  
   - **ETF vs. Spot**: Convert IBIT closing price into BTC (1,755.1 shares per BTC) and subtract spot price.  
   - **Futures vs. Spot**: Subtract spot price from futures closing price.  

2. **Z-Score Computation**  
   \[  
     z = \frac{\text{current spread} - \mu}{\sigma}  
   \]  
   - **Entry**:  
     - `z > +1`: Short futures/ETF, long spot  
     - `z < -1`: Long futures/ETF, short spot  
   - **Exit**:  
     - `-0.5 ≤ z ≤ 0.5` (mean reversion)  
     - 48-hour time stop  
     - `|z| ≥ 2` (stop-loss)  

3. **Position Sizing**  
   - Dollar-neutral notional matching  
   - Volume-based scaling:  
     - If daily volume > 2× 20-day avg → +20% size  
     - If volume < 0.5× avg → size ↓ proportionally  

## Data Processing

- **Period**: January – March 2025  
- **Training**: First 40 trading days to compute mean (μ) and standard deviation (σ) of spreads  
- **Testing**: Next 20 trading days for out-of-sample evaluation using pre-computed μ and σ  

## Trade Execution

- Split orders across multiple exchanges to access best liquidity  
- Execute during high-liquidity windows for tighter spreads  
- Pause if bid–ask spread > 2× rolling average to avoid slippage  

## Backtesting

Train on the first two months of data; test on the third month (out-of-sample). Z-scores from the training set drive entry/exit signals, mitigating data-mining bias.

## Results

| Leg             | Notional Traded    | Profit       | Return | # Trades | Win Rate | Max Profit  | Max Loss    |
|-----------------|--------------------|--------------|--------|----------|----------|-------------|-------------|
| ETF vs Spot     | $10,174,767.22     | $80,369.76   | 0.79%  | 3        | 100%     | $31,274.08  | $23,322.20  |
| Futures vs Spot | $10,215,097.60     | $98,886.20   | 0.97%  | 3        | 100%     | $39,515.00  | N/A         |
