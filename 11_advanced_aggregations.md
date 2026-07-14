# Pandas Advanced Aggregations and Pivoting Table Layouts

Data pipelines routinely require aggregating fine-grained transactional metrics into high-level business summaries. Beyond standard single-column summaries, data engineers must execute multi-metric rollups using the `.agg()` framework and reshape matrix dimensions using structural pivots.

---

## 1. Multi-Metric Rollups (`.groupby().agg()`)

The `.agg()` method allows you to calculate completely different statistical measurements across multiple columns simultaneously within a single grouping operation.

### Syntax Architecture

Pass named arguments into the `.agg()` function where each argument maps a target column to a specific mathematical operator string (e.g., `'sum'`, `'mean'`, `'max'`, `'count'`).

```python
    summary_df = df.groupby('group_column').agg(
        total_metric=('target_column_1', 'sum'),
        average_metric=('target_column_2', 'mean')
    ).reset_index()
```

**Example:**

```python
    import pandas as pd

    data = {
        'region': ['MUM', 'MUM', 'DEL', 'DEL'],
        'data_gb': [100, 250, 80, 120],
        'latency_ms': [12.5, 14.2, 8.5, 9.1]
    }
    df = pd.DataFrame(data)

    # Calculate total data volume and maximum latency per region simultaneously
    region_metrics = df.groupby('region').agg(
        total_gb=('data_gb', 'sum'),
        peak_latency=('latency_ms', 'max')
    ).reset_index()
    
    print(region_metrics)
```

**Output:**

```
      region  total_gb  peak_latency
    0    DEL       200           9.1
    1    MUM       350          14.2
```
---

## 2. Reshaping Matrix Layouts (`.pivot_table()`)

The `.pivot_table()` method rotates a DataFrame's layout structure. It transforms unique row entries from a column into completely distinct, horizontal column headers, aggregating data across the intersection coordinates.

### Core Parameter Controls

* **`index`**: The column whose unique values will form the vertical rows of the new table.
* **`columns`**: The column whose unique values will rotate horizontally to become the new column headers.
* **`values`**: The numeric column to be aggregated at the intersection of each row and column.
* **`aggfunc`**: The mathematical operation used to combine values (defaults to `'mean'`).



**Example:**

```python
    import pandas as pd

    data = {
        'year': [2025, 2025, 2026, 2026],
        'env': ['PROD', 'DEV', 'PROD', 'DEV'],
        'cost': [5000, 1200, 7500, 1800]
    }
    df = pd.DataFrame(data)

    # Pivot the environment names horizontally to track cost scaling per year
    cost_pivot = df.pivot_table(
        index='year', 
        columns='env', 
        values='cost', 
        aggfunc='sum'
    ).reset_index()
    
    print(cost_pivot)
```

**Output:**

```
    env  year   DEV  PROD
    0    2025  1200  5000
    1    2026  1800  7500
```
