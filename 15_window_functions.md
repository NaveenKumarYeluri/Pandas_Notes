# Pandas Window Functions, Shifts, and Rolling Analytics

Data engineering pipelines frequently require calculating contextual metrics that depend on neighboring rows. Instead of writing inefficient Python loops, Pandas provides vectorized methods to shift row alignments, calculate aggregates across moving windows, and compute running totals.

---

## 1. Shifting Rows for Comparison (`.shift()`)

The `.shift()` method moves a column's data vector up or down by a specified number of rows. This allows you to compare values in the current row directly against historical values in a previous row.

### Core Parameter Controls

* **`periods`**: The number of rows to shift. Positive values move data down (fetching past rows); negative values move data up (fetching future rows).

### Shift Execution Example

```python
    import pandas as pd

    df = pd.DataFrame({
        'job_id': [101, 102, 103],
        'status': ['START', 'RUNNING', 'STOP']
    })

    # Pull the previous row's status down into the current row context
    df['previous_status'] = df['status'].shift(periods=1)
    print(df)
```

**Output:**

```
       job_id   status previous_status
    0     101    START            None
    1     102  RUNNING           START
    2     103     STOP         RUNNING
```

---

## 2. Moving Window Aggregations (`.rolling()`)

The `.rolling()` method creates a moving window over a specified number of sequential rows. Once the window is created, you chain an aggregation function (like `.mean()` or `.sum()`) to compute a rolling statistic.

### Core Parameter Controls

* **`window`**: The size of the moving window expressed as a number of rows.
* **`min_periods`**: The minimum number of valid observations required in the window to output a value (otherwise returns `NaN`).

### Rolling Window Example

```python
    import pandas as pd

    df = pd.DataFrame({
        'hour': [1, 2, 3, 4],
        'load_mb': [100, 200, 150, 300]
    })

    # Calculate a 2-row rolling average load window
    df['rolling_avg_load'] = df['load_mb'].rolling(window=2, min_periods=1).mean()
    print(df)
```

**Output:**

```
       hour  load_mb  rolling_avg_load
    0     1      100             100.0
    1     2      200             150.0
    2     3      150             175.0
    3     4      300             225.0
```

---

## 3. Cumulative Running Metrics (`.cumsum()` / `.cummax()`)

Cumulative operations keep an ongoing mathematical running tally from the first row down to the last row of the dataset.

### Core Cumulative Operations

* **`.cumsum()`**: Calculates the continuous running total sum.
* **`.cummax()`**: Tracks the highest value encountered in the data vector up to that specific row coordinate.
* **`.cummin()`**: Tracks the lowest value encountered in the data vector up to that specific row coordinate.
* **`.cumprod()`**: Calculates the continuous running total product.

### Cumulative Running Summary Example

```python
    import pandas as pd

    df = pd.DataFrame({
        'batch_id': [1, 2, 3],
        'cost_usd': [12.50, 8.00, 15.20]
    })

    # Calculate the operational running total spend across rows
    df['running_total_spend'] = df['cost_usd'].cumsum()
    print(df)
```

**Output:**

```
       batch_id  cost_usd  running_total_spend
    0         1      12.5                 12.5
    1         2       8.0                 20.5
    2         3      15.2                 35.7
```
