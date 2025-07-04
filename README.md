# Advanced Pairs Trading Strategy with Kalman Filter

## Overview

This project implements a sophisticated **pairs trading strategy** that combines traditional statistical arbitrage techniques with advanced Kalman filtering for dynamic hedge ratio estimation. Pairs trading is a market-neutral strategy that profits from the relative price movements between two correlated securities, regardless of overall market direction.

## Strategy Foundation

### Core Concept

Pairs trading exploits temporary price divergences between two historically correlated securities. The strategy operates on the **mean reversion principle** - when the price relationship between two assets deviates from its historical norm, it's expected to eventually revert to the mean. This creates arbitrage opportunities where traders can profit from the convergence.

### Mathematical Framework

The strategy is built on the concept of **cointegration** - a statistical relationship where two non-stationary time series have a stationary linear combination. If two assets are cointegrated, their price spread follows a mean-reverting process:

\$ Spread(t) = P_1(t) - \beta \cdot P_2(t) \$

Where $\beta$ is the hedge ratio that makes the spread stationary.

## Implementation Components

### 1. Pair Selection and Cointegration Testing

The strategy begins by identifying suitable pairs from financial services stocks using the **Engle-Granger cointegration test**. The implementation:

- Downloads historical price data for 16 financial services stocks
- Performs pairwise cointegration tests using the `coint()` function
- Creates a cointegration matrix to visualize relationships
- Selects pairs with p-values < 0.02, indicating strong cointegration

**Key Function:**

```python
def cointegration_checker(stock_dataframe):
    # Tests all possible pairs for cointegration
    # Returns p-values matrix and cointegrated pairs
```

### 2. Spread Construction and Analysis

Once a cointegrated pair is identified (Citigroup and State Street Corporation), the strategy constructs the spread using **Ordinary Least Squares (OLS) regression**:

- Calculates the hedge ratio through linear regression
- Constructs the spread: `spread = Asset_2 - (coef * Asset_1)`
- Analyzes spread stationarity using the **Augmented Dickey-Fuller (ADF) test**

The ADF test confirms that the spread is stationary (p-value < 0.05), validating the mean-reverting assumption

### 3. Traditional Signal Generation

The baseline approach uses **Z-score normalization** for signal generation:

\$ Z-score = \frac{Price Ratio - \mu}{\sigma} \$

**Signal Rules:**

- **Buy signal**: Z-score < -1.25 (spread undervalued)
- **Sell signal**: Z-score > +1.25 (spread overvalued)
- **Exit signal**: |Z-score| < 0.5 (mean reversion)

### 4. Kalman Filter Enhancement

The advanced implementation incorporates a **Kalman filter** to dynamically estimate the hedge ratio, providing several advantages over static methods:

**Key Benefits:**

- **Adaptive estimation**: Continuously updates hedge ratio as market conditions change
- **Noise reduction**: Filters out market noise for cleaner signals
- **Memory efficiency**: Only requires previous state information
- **Real-time adaptation**: Responds to structural breaks in the relationship

**Kalman Filter Implementation:**

```python
def kalman_filter_pairs(data_1, data_2, Q=0.1, R=0.1):
    # Q: Process noise covariance
    # R: Measurement noise covariance
    # Returns filtered spread with dynamic hedge ratio
```

### 5. Parameter Optimization

The strategy employs **Bayesian Optimization** to find optimal Kalman filter parameters:

- Optimizes Q (process noise) and R (measurement noise) parameters
- Maximizes Sharpe ratio as the objective function
- Uses 5 initial points and 10 iterations for parameter search

### 6. Advanced Signal Generation

With the Kalman-filtered spread, the strategy generates more sophisticated signals:

- Calculates rolling Z-scores using 10-day and 60-day moving averages
- Implements threshold-based entry/exit rules
- Uses dynamic thresholds based on rolling standard deviation

## Trading Simulation and Backtesting

### Position Sizing and Risk Management

The trading simulation implements realistic position sizing and risk management:

- **Capital allocation**: \$25,000 total leverage split equally between assets
- **Stop-loss mechanism**: Maximum \$500 loss per trade
- **Dynamic position tracking**: Continuously monitors open positions
- **Transaction cost consideration**: Realistic trade execution modeling

### Performance Metrics

The backtesting framework calculates comprehensive performance statistics:

- **Sharpe Ratio**: Risk-adjusted returns measurement
- **Maximum Drawdown**: Peak-to-trough decline assessment
- **Hit Rate**: Percentage of profitable trades
- **CAGR**: Compound Annual Growth Rate
- **Volatility**: Strategy return standard deviation

## Strategy Advantages

### Market Neutrality

The strategy is **market-neutral**, generating profits regardless of overall market direction. This provides:

- **Reduced systematic risk**: Protection against market-wide movements
- **Consistent performance**: Potential profits in bull, bear, or sideways markets
- **Portfolio diversification**: Low correlation with traditional long-only strategies

### Statistical Foundation

The approach is grounded in robust statistical methods:

- **Cointegration theory**: Ensures long-term equilibrium relationship
- **Mean reversion**: Exploits temporary price dislocations
- **Dynamic adaptation**: Kalman filter adjusts to changing market conditions

### Risk Management

Built-in risk controls include:

- **Hedged positions**: Long and short positions offset each other
- **Stop-loss protection**: Limits maximum loss per trade
- **Position sizing**: Controls exposure to individual trades

## Strategy Limitations and Risks

### Correlation Breakdown

The primary risk is **correlation breakdown** between the paired assets

- Historical relationships may not persist in the future
- Structural changes can permanently alter price relationships
- Economic shocks can disrupt cointegration

### Model Risk

Several model-related challenges exist:

- **Parameter sensitivity**: Kalman filter performance depends on Q and R parameters
- **Overfitting**: Historical optimization may not translate to future performance
- **Look-ahead bias**: Potential data snooping in parameter selection

### Execution Challenges

Practical implementation faces several hurdles:

- **Transaction costs**: Frequent trading can erode profits
- **Slippage**: Market impact of simultaneous trades
- **Liquidity constraints**: Difficulty executing large positions

### Limited Opportunities

The strategy has inherent limitations:

- **Scarcity of pairs**: Finding strongly cointegrated pairs is challenging
- **Timing requirements**: Must be among first to capitalize on opportunities
- **Market efficiency**: Arbitrage opportunities may be quickly eliminated

### Statistical Assumptions

The approach relies on several assumptions that may not hold:

- **Stationarity**: Spread must remain mean-reverting over time
- **Normal distribution**: Z-score calculations assume normal spread distribution
- **Linear relationships**: OLS regression assumes linear cointegration

## Risk Management Considerations

### Stop-Loss Implementation

The strategy incorporates sophisticated stop-loss mechanisms:

- **Threshold-based stops**: Exit when Z-score exceeds 3-sigma levels
- **Dollar-based stops**: Maximum \$500 loss per trade
- **Time-based stops**: Prevent indefinite position holding

### Position Sizing

Conservative position sizing helps manage risk:

- **Equal weighting**: 50/50 split between long and short positions
- **Leverage limits**: Maximum \$25,000 total exposure
- **Diversification**: Focus on single sector reduces diversification benefits

## Future Enhancements

Potential improvements to the strategy include:

- **Multi-pair trading**: Implementing portfolio of multiple pairs
- **Machine learning**: Using ML algorithms for pair selection and signal generation
- **Alternative data**: Incorporating news sentiment and alternative datasets
- **Dynamic thresholds**: Adaptive entry/exit levels based on market volatility
- **Transaction cost modeling**: More realistic cost assumptions

## Conclusion

This pairs trading implementation represents a sophisticated approach to statistical arbitrage, combining traditional cointegration analysis with modern Kalman filtering techniques. While the strategy offers compelling advantages including market neutrality and statistical robustness, successful implementation requires careful attention to risk management, parameter optimization, and realistic expectations about performance limitations.

The integration of Kalman filtering provides a significant enhancement over static hedge ratio methods, enabling dynamic adaptation to changing market conditions. However, practitioners must remain aware of the inherent risks including correlation breakdown, model risk, and execution challenges when deploying this strategy in live trading environments.
