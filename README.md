Statistics Pattern Recognition and Stock Prediction

# Stock Prediction & Pattern Recognition

This project implements a statistical pattern recognition pipeline for stock market analysis. The core logic is encapsulated in `stock_slice_v1.5.ipynb`, which processes historical stock data to generate training datasets for machine learning models.

## Overview

The system processes daily stock data (Adjusted Close and Volume) using a sliding window approach to create normalized feature sets and corresponding future performance targets relative to the S&P 500.

## Data Processing Pipeline

The `sliding_window_iter_array` function is the engine of this pipeline. It operates as follows:

### 1. Configuration Parameters
-   **Window Size**: 120 days (approx. 6 months of trading days).
-   **Step Size**: 5 days (the window slides forward by 1 week).
-   **Prediction Horizon**: 120 days into the future.

### 2. Feature Engineering (X)
For each window of 120 days, the data is normalized to be scale-invariant:

-   **Price Normalization**:
    Prices ($P$) are scaled based on the window's min ($P_{min}$) and max ($P_{max}$) values:
    $$P_{norm} = \frac{P - P_{min}}{P_{max} - P_{min}} \times \frac{P_{max}}{P_{min}}$$
    *This preserves the shape of the price movement while normalizing the absolute range.*

-   **Volume Normalization**:
    Volume ($V$) is normalized by the mean volume of the window ($V_{mean}$):
    $$V_{norm} = \frac{V}{V_{mean}}$$

### 3. Target Generation (Y)
The target variable is the **relative performance** of the stock compared to the S&P 500 index at specific future intervals.

-   **Future Timepoints**: The script selects 5 specific future days relative to the end of the window:
    -   Day 1
    -   Day 5 (1 week)
    -   Day 10 (2 weeks)
    -   Day 30 (1 month)
    -   Day 120 (6 months)
    *(Indices: 0, 4, 9, 29, 118)*

-   **Relative Performance Formula**:
    $$Y = \frac{Stock_{future} / Stock_{current}}{SP500_{future} / SP500_{current}}$$
    *A value > 1.0 indicates the stock outperformed the market.*

### 4. Data Filtering
Windows are discarded if:
-   They contain `NaN` values.
-   The price range is effectively zero (flat line).
-   The volume is effectively zero.
-   Future data is missing or incomplete.
-   Post-processing: Samples with values > 50 are filtered out as outliers.

## Output Data Structure

The notebook generates two NumPy arrays saved as `.npy` files:

-   **`x_concat.npy`**: The input features.
    -   **Shape**: `(N, 120, 2)`
    -   **Dimensions**: `[Number of Samples, Time Steps (120), Features (2)]`
    -   **Features**: Index 0 = Normalized Price, Index 1 = Normalized Volume.

-   **`y_concat.npy`**: The target variables.
    -   **Shape**: `(N, 5)`
    -   **Dimensions**: `[Number of Samples, Future Timepoints (5)]`

## Visualization

The project includes visualization capabilities (via `stock_plot.py` logic) to generate candlestick charts with technical indicators:

-   **MACD (Moving Average Convergence Divergence)**:
    -   Fast EMA: 12 periods
    -   Slow EMA: 26 periods
    -   Signal Line: 9 periods
-   **Stochastic Oscillator**:
    -   %K Window: 14 periods
    -   %D Window: 3 periods
-   **Buy Signals**:
    -   Marked when the Close is higher than Open, and the previous day's Close was lower than its Open (Bullish Engulfing-like pattern).

## Requirements

-   Python 3.x
-   `numpy`
-   `pandas`
-   `matplotlib`
-   `mplfinance`
-   `scikit-learn`
-   `Pillow`

## Usage Instructions

1.  **Data Placement**:
    -   Place stock CSV files in: `stock_market_data/nasdaq/csv/`
    -   Place `SPY_yahoo.csv` in: `stock_market_data/`
2.  **Execution**:
    -   Run `stock_slice_v1.5.ipynb`.
    -   The script will process the tickers, print progress, and save the `.npy` files to the current directory.



# 1.Traning data:
    1. Window_Step_size
    2. Window_size
    3. Price only & price + volume together 
    4. Data selection:
        1. all date   
        2. all date before 2000
        3. all date before 2010
        4. all date before 2020
        5. all date after 2015

# 2. fitting model comparsion:
       1. TSlearn libary
       2. Kmean from Sklearn
       3. DTW Kmean

# 3.Output Comparsion:
    1. which classifiation/shape is the best 
    2. 1 day 5 day 10 day performance 
    3. whether Alpha / Beta. whether it is win over market 
    4. sampling from all data/ sampling from older data/ sampling from newer data
    5. test sample distribution and whole dataset distribution

    




