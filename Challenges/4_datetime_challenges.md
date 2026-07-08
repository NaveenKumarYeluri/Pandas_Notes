# Pandas Datetime Processing Practice Challenges

This document contains six progressive data engineering challenges designed to test string-to-datetime parsing, component attribute extraction, chronological boundary filtering, and temporal aggregation pipelines.

---

## Challenge 1: The Server Log Ingestion Audit (Type Conversion)

**Scenario:** An application monitoring daemon outputs an emergency telemetry dump as a list of dictionaries. Before any time-series sorting can occur, the text-based timestamps must be parsed into matching data types.

### Raw Data Input

```python
    import pandas as pd

    telemetry = {
        'event_id': [3001, 3002, 3003],
        'timestamp_str': ['2026-07-01 23:55:01', '2026-07-02 00:05:12', '2026-07-02 01:20:00']
    }
    df_log = pd.DataFrame(telemetry)
```

### Your Tasks:

1. Print the baseline data types (`.dtypes`) of `df_log`.
2. Cast the `timestamp_str` column into a proper `datetime64[ns]` object type using `pd.to_datetime()`. Save it to a new column named `timestamp_parsed`.
3. Print the updated `.dtypes` grid to verify the transformation state.

### My Solution:

```python
    import pandas as pd

    telemetry = {
        "event_id": [3001, 3002, 3003],
        "timestamp_str": [
            "2026-07-01 23:55:01",
            "2026-07-02 00:05:12",
            "2026-07-02 01:20:00",
        ],
    }
    df_log = pd.DataFrame(telemetry)

    print("Datatypes before coversion:\n", df_log.dtypes, "\n")  # Question 1

    df_log["timestamp_parsed"] = pd.to_datetime(
        df_log["timestamp_str"]
    )  # Question 2

    print("Datatypes after coversion:\n", df_log.dtypes)  # Question 3
```

### My Output Verification:

```
    event_id          int64
    timestamp_str    object
    dtype: object 

    Datatypes after coversion:
    event_id                     int64
    timestamp_str               object
    timestamp_parsed    datetime64[ns]
    dtype: object
```

---

## Challenge 2: Chronological Dimension Extraction (Feature Engineering)

**Scenario:** A data warehouse staging pipeline requires enriching raw transactional records with separate date category features to support partition indexing across downstream OLAP analytical buckets.

### Raw Data Input

```python
    import pandas as pd

    orders = {
        'order_id': [5001, 5002, 5003],
        'order_time': pd.to_datetime(['2026-03-14 08:30:00', '2026-06-21 15:45:22', '2026-11-05 22:12:05'])
    }
    df_orders = pd.DataFrame(orders)
```

### Your Tasks:

1. Extract the calendar month index number from the `order_time` vector and store it inside a column named `order_month`.
2. Extract the name of the specific day of the week (e.g., 'Monday') from the `order_time` vector and store it inside a column named `day_of_week`.
3. Print the modified DataFrame to inspect the newly derived features.

### My Solution:

```python
    import pandas as pd

    orders = {
        "order_id": [5001, 5002, 5003],
        "order_time": pd.to_datetime(
            ["2026-03-14 08:30:00", "2026-06-21 15:45:22", "2026-11-05 22:12:05"]
        ),
    }
    df_orders = pd.DataFrame(orders)

    df_orders["order_month"] = df_orders["order_time"].dt.month  # Question 1

    df_orders["day_of_week"] = df_orders["order_time"].dt.day_name()  # Question 2

    print("Orders:\n", df_orders)  # Question 3
```

### My Output Verification:

```
    Orders:
       order_id          order_time  order_month day_of_week
    0      5001 2026-03-14 08:30:00            3    Saturday
    1      5002 2026-06-21 15:45:22            6      Sunday
    2      5003 2026-11-05 22:12:05           11    Thursday
```

---

## Challenge 3: Financial Quarter Boundary Isolation (Row Filtering)

**Scenario:** An internal audit requires isolating transaction rows that occurred strictly within a specific business timeframe window. You must build a logical mask to drop items outside the operational cutoff bounds.

### Raw Data Input

```python
    import pandas as pd

    ledger = {
        'tx_id': [9901, 9902, 9903, 9904],
        'tx_date': pd.to_datetime(['2026-05-28', '2026-06-30', '2026-07-01', '2026-07-15'])
    }
    df_ledger = pd.DataFrame(ledger)
```

### Your Tasks:

1. Apply a boolean row filter to isolate records where the `tx_date` occurs **on or after July 1, 2026 (`2026-07-01`)**.
2. Assign the matching rows to a new variable called `df_h2_ledger` and print the resulting table.

### My Solution:

```python
    import pandas as pd

    ledger = {
        "tx_id": [9901, 9902, 9903, 9904],
        "tx_date": pd.to_datetime(
            ["2026-05-28", "2026-06-30", "2026-07-01", "2026-07-15"]
        ),
    }
    df_ledger = pd.DataFrame(ledger)

    df_h2_ledger = df_ledger[df_ledger["tx_date"] >= "2026-07-01"]  # Question 1

    print("Output:\n", df_h2_ledger)  # Question 2
```

### My Output Verification:

```
    Output:
       tx_id    tx_date
    2   9903 2026-07-01
    3   9904 2026-07-15
```

---

## Challenge 4: High-Frequency Server Peak Analysis (Temporal Filtering)

**Scenario:** A security gateway tracks access requests. To evaluate traffic anomalies, you must isolate a micro-window of logs targeting operations that occurred specifically during the late-night hour of **23:00 to 23:59**.

### Raw Data Input

```python
    import pandas as pd

    access_stream = {
        'request_id': ['REQ-01', 'REQ-02', 'REQ-03', 'REQ-04'],
        'access_time': pd.to_datetime(['2026-07-07 10:15:00', '2026-07-07 23:12:45', '2026-07-07 23:58:12', '2026-07-08 00:02:10'])
    }
    df_access = pd.DataFrame(access_stream)
```

### Your Tasks:

1. Extract the hour integer vector from the `access_time` column using the `.dt` accessor space.
2. Build a row selection mask that filters `df_access` to retain only rows where the extracted hour is **strictly equal to 23**.

### My Solution:

```python
    import pandas as pd
    
    access_stream = {
        "request_id": ["REQ-01", "REQ-02", "REQ-03", "REQ-04"],
        "access_time": pd.to_datetime(
            [
                "2026-07-07 10:15:00",
                "2026-07-07 23:12:45",
                "2026-07-07 23:58:12",
                "2026-07-08 00:02:10",
            ]
        ),
    }
    df_access = pd.DataFrame(access_stream)
    
    # df_access["access_hour"] = df_access["access_time"].dt.hour
    
    # When you want to extract only few columns then pass a list
    # print(
    #     "Output:\n",
    #     df_access[df_access["access_hour"] == 23][["request_id", "access_time"]],
    # )
    
    # # Method 1: creating a new column and then filter
    # df_access["access_hour"] = df_access["access_time"].dt.hour  # Question 1
    
    # print(
    #     "Method 1 output:\n",
    #     df_access[df_access["access_hour"] == 23],  # Question 2
    # )
    
    # Method 2: direct filter without creating new column
    print(
        "Method 2 output:\n",
        df_access[df_access["access_time"].dt.hour == 23],  # Question 1, 2
    )
```

### My Output Verification:

```
    Method 2 output:
      request_id         access_time
    1     REQ-02 2026-07-07 23:12:45
    2     REQ-03 2026-07-07 23:58:12
```

---

## Challenge 5: Periodic Revenue Summaries (Temporal GroupBy)

**Scenario:** An e-commerce system captures global transactions. To populate an executive dashboard, you must roll up high-frequency row items into standardized monthly cash summaries.

### Raw Data Input

```python
    import pandas as pd

    sales_stream = {
        'settlement_date': pd.to_datetime(['2026-01-05', '2026-01-20', '2026-02-14', '2026-02-28', '2026-03-03']),
        'gross_revenue': [12000, 8500, 23000, 11000, 45000]
    }
    df_sales = pd.DataFrame(sales_stream)
```

### Your Tasks:

1. Generate a temporary dimension column named `sales_month` that captures the numeric month identifier from `settlement_date`.
2. Group the DataFrame by `sales_month` and calculate the cumulative **sum** of the `gross_revenue` column.
3. Reset the structure's index to flatten the output table, making `sales_month` a standard column vector again.

### My Solution:

```python
    import pandas as pd

    sales_stream = {
        "settlement_date": pd.to_datetime(
            ["2026-01-05", "2026-01-20", "2026-02-14", "2026-02-28", "2026-03-03"]
        ),
        "gross_revenue": [12000, 8500, 23000, 11000, 45000],
    }
    df_sales = pd.DataFrame(sales_stream)

    df_sales["sales_month"] = df_sales["settlement_date"].dt.month

    gross_revenue_per_month = (
        df_sales.groupby("sales_month")["gross_revenue"].sum().reset_index()
    )

    print("Gross revenue per month:\n", gross_revenue_per_month, "\n")
    print("Our DF:\n", df_sales)
```

### My Output Verification:

```
       sales_month  gross_revenue
    0            1          20500
    1            2          34000
    2            3          45000 

    Our DF:
      settlement_date  gross_revenue  sales_month
    0      2026-01-05          12000            1
    1      2026-01-20           8500            1
    2      2026-02-14          23000            2
    3      2026-02-28          11000            2
    4      2026-03-03          45000            3
```

---

## Challenge 6: The Automated Batch Ingestion Pipeline (The Final Boss)

**Scenario:** A multi-region storage cluster drops unformatted streaming logs. Before loading this operational data into a downstream data warehouse, you must execute a complete sanitization pipeline to parse dates, extract time metrics, filter on specific execution dates, and group metrics by processing centers.

### Raw Data Input

```python
    import pandas as pd

    raw_logs = {
        'job_id': [11001, 11002, 11003, 11004, 11005],
        'region': ['Mumbai', 'Delhi', 'Mumbai', 'Delhi', 'Noida'],
        'execution_time': ['2026-07-06 14:20:00', '2026-07-06 18:35:12', '2026-07-07 09:10:00', '2026-07-07 11:45:00', '2026-07-07 16:22:30'],
        'data_processed_gb': [45, 120, 15, 88, 30]
    }
    df_raw = pd.DataFrame(raw_logs)
```

### Your Tasks:

1. Parse the Temporal Layer: Convert the `execution_time` string column into an active `datetime64[ns]` object column.
2. Temporal Window Cutoff: Filter the rows to retain *only* operations that occurred on or after **July 7, 2026 (`2026-07-07`)**.
3. Hour Extraction: On this filtered subset, extract the specific hour component and save it to a new column named `start_hour`.
4. Multi-Metric Rollup: Group the sanitized subset by the `region` column and use the `.agg()` method to calculate two metrics simultaneously:
   * The **sum** of all values in the `data_processed_gb` column (rename it to `total_gb`).
   * The **max** (highest peek hour) value found inside your newly derived `start_hour` column (rename it to `peak_hour`).
5. Matrix Flattening: Reset the structural layout index so the grouping key turns back into a standard column vector before final database ingestion.

### My Solution:

```python
    import pandas as pd

    raw_logs = {
        "job_id": [11001, 11002, 11003, 11004, 11005],
        "region": ["Mumbai", "Delhi", "Mumbai", "Delhi", "Noida"],
        "execution_time": [
            "2026-07-06 14:20:00",
            "2026-07-06 18:35:12",
            "2026-07-07 09:10:00",
            "2026-07-07 11:45:00",
            "2026-07-07 16:22:30",
        ],
        "data_processed_gb": [45, 120, 15, 88, 30],
    }
    df_raw = pd.DataFrame(raw_logs)

    df_raw["execution_time"] = pd.to_datetime(
        df_raw["execution_time"]
    )  # Question 1

    df_filtered = df_raw[
        df_raw["execution_time"] >= "2026-07-07"
    ].copy()  # Question 2

    df_filtered["start_hour"] = df_filtered["execution_time"].dt.hour  # Question 3

    metrics = (
        df_filtered.groupby("region")
        .agg(
            total_gb=("data_processed_gb", "sum"), peak_hour=("start_hour", "max")
        )
        .reset_index()
    )  # Question 4, 5

    print("Aggregated Metrics:\n", metrics)
```

### My Output Verification:

```
    Aggregated Metrics:
       region  total_gb  peak_hour
    0   Delhi        88         11
    1  Mumbai        15          9
    2   Noida        30         16
```
