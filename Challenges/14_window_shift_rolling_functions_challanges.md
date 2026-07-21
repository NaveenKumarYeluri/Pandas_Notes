# Pandas Window Functions and Rolling Analytics Practice Challenges

This document contains six progressive data engineering challenges designed to test row shifting, rolling window analytics, cumulative metrics, and multi-layered chronological analysis.

---

## Challenge 1: Log Sequence Comparison (Basic Row Shifting)

**Scenario:** A cloud security scanner outputs a sequence of access event codes. To map behavioral changes over time, you must compare each log's action directly against the event that immediately preceded it.

### Raw Data Input

```python
    import pandas as pd

    security_logs = {
        'log_id': [88001, 88002, 88003, 88004],
        'action': ['LOGIN', 'ACCESS_DENIED', 'ACCESS_DENIED', 'LOGOUT']
    }
    df_security = pd.DataFrame(security_logs)
```

### Your Tasks:

1. Use the `.shift()` method to copy the `action` value of the **previous row** down into the current row vector.
2. Save this shifted data array into a new column handle named `previous_action`.
3. Print the resulting DataFrame.

### My Solution:

```python
    import pandas as pd

    security_logs = {
        "log_id": [88001, 88002, 88003, 88004],
        "action": ["LOGIN", "ACCESS_DENIED", "ACCESS_DENIED", "LOGOUT"],
    }
    df_security = pd.DataFrame(security_logs)

    df_security["previous_action"] = df_security["action"].shift(periods=1)
    print(df_security)
```

### My Output Verification:

```
       log_id         action previous_action
    0   88001          LOGIN            None
    1   88002  ACCESS_DENIED           LOGIN
    2   88003  ACCESS_DENIED   ACCESS_DENIED
    3   88004         LOGOUT   ACCESS_DENIED
```

---

## Challenge 2: Network Traffic Smoothing (Basic Rolling Window)

**Scenario:** An edge gateway tracks data traffic bursts every minute. Raw monitoring charts are too erratic, so you must calculate a 3-row moving average to smooth out localized metric spikes.

### Raw Data Input

```python
    import pandas as pd

    gateway_traffic = {
        'minute': [1, 2, 3, 4, 5],
        'throughput_mb': [120, 450, 300, 150, 600]
    }
    df_traffic = pd.DataFrame(gateway_traffic)
```

### Your Tasks:

1. Initialize a moving window of **3 rows** on the `throughput_mb` column using `.rolling()`.
2. Configure the system to output structural calculations even if only **1 row** is present in the window window slice.
3. Compute the **mean** average across this moving window, save it to `rolling_avg_mb`, and print the grid frame.

### My Solution:

```python
    import pandas as pd

    gateway_traffic = {"minute": [1, 2, 3, 4, 5], "throughput_mb": [120, 450, 300, 150, 600]}
    df_traffic = pd.DataFrame(gateway_traffic)

    df_traffic["rolling_avg_mb"] = (
        df_traffic["throughput_mb"].rolling(window=3, min_periods=1).mean()
    )
    print(df_traffic)
```

### My Output Verification:

```
       minute  throughput_mb  rolling_avg_mb
    0       1            120           120.0
    1       2            450           285.0
    2       3            300           290.0
    3       4            150           300.0
    4       5            600           350.0
```

---

## Challenge 3: Financial Burn Rate Tracking (Cumulative Running Sum)

**Scenario:** A server farm accumulates hosting charges continuously as compute nodes execute queries. Management needs a dashboard metric that tracks the exact running total budget spend calculated dynamically row-by-row.

### Raw Data Input

```python
    import pandas as pd

    billing_stream = {
        'invoice_id': ['INV-01', 'INV-02', 'INV-03', 'INV-04'],
        'charge_usd': [15.00, 42.50, 8.00, 120.25]
    }
    df_billing = pd.DataFrame(billing_stream)
```

### Your Tasks:

1. Apply the vectorized cumulative sum operator to calculate the sequential running tally of `charge_usd`.
2. Store the mathematical outcome vector in a column named `cumulative_spend_usd`.
3. Print the resulting tracking grid matrix table view.

### My Solution:

```python
    import pandas as pd

    billing_stream = {
        "invoice_id": ["INV-01", "INV-02", "INV-03", "INV-04"],
        "charge_usd": [15.00, 42.50, 8.00, 120.25],
    }
    df_billing = pd.DataFrame(billing_stream)

    df_billing["cumulative_spend_usd"] = df_billing["charge_usd"].cumsum()
    print(df_billing)
```

### My Output Verification:

```
      invoice_id  charge_usd  cumulative_spend_usd
    0     INV-01       15.00                 15.00
    1     INV-02       42.50                 57.50
    2     INV-03        8.00                 65.50
    3     INV-04      120.25                185.75
```

---

## Challenge 4: Peak Infrastructure High-Water Mark (Cumulative Max)

**Scenario:** A cluster orchestration tool tracks active thread workloads over time. To ensure optimal hardware headroom, you must build a tracking column that continuously records the highest workload spike encountered up to that moment.

### Raw Data Input

```python
    import pandas as pd

    cluster_telemetry = {
        'check_interval': [1, 2, 3, 4, 5],
        'active_threads': [150, 120, 450, 380, 520]
    }
    df_cluster = pd.DataFrame(cluster_telemetry)
```

### Your Tasks:

1. Use a vectorized cumulative tracking method on `active_threads` to map the highest value encountered up to each row coordinate.
2. Save this historical peak vector in a column named `peak_high_water_mark`.
3. Print the updated analytical summary grid frame.

### My Solution:

```python
    import pandas as pd

    cluster_telemetry = {
        "check_interval": [1, 2, 3, 4, 5],
        "active_threads": [150, 120, 450, 380, 520],
    }
    df_cluster = pd.DataFrame(cluster_telemetry)

    df_cluster["peak_high_water_mark"] = df_cluster["active_threads"].cummax()
    print(df_cluster)
```

### My Output Verification:

```
       check_interval  active_threads  peak_high_water_mark
    0               1             150                   150
    1               2             120                   150
    2               3             450                   450
    3               4             380                   450
    4               5             520                   520
```

---

## Challenge 5: Inter-Row Step Latency Analysis (Time Delta Shifts)

**Scenario:** A workflow scheduler registers task updates down a streaming staging grid. To calculate the processing delay duration between independent consecutive tasks, you must evaluate the time gap between the current task's start window and the previous task's start window.

### Raw Data Input

```python
    import pandas as pd

    task_stream = {
        'task_id': [501, 502, 503, 504],
        'started_at_str': ['2026-07-18 08:00:00', '2026-07-18 08:05:30', '2026-07-18 08:12:00', '2026-07-18 08:30:15']
    }
    df_tasks = pd.DataFrame(task_stream)
```

### Your Tasks:

1. Transform `started_at_str` into a true nanosecond timestamp array (`datetime64[ns]`).
2. Use `.shift()` to fetch the previous row's timestamp value down into a new tracking lane column named `prev_task_time`.
3. Subtract `prev_task_time` from your current timestamp vector to compute the duration gap. Save it to a column named `time_since_last_task`.
4. Use the `.dt` property namespace to convert the duration gap entirely into standard decimal **seconds** (save to a column named `gap_seconds`). Print the table.

### My Solution:

```python
    import pandas as pd

    task_stream = {
        "task_id": [501, 502, 503, 504],
        "started_at_str": [
            "2026-07-18 08:00:00",
            "2026-07-18 08:05:30",
            "2026-07-18 08:12:00",
            "2026-07-18 08:30:15",
        ],
    }
    df_tasks = pd.DataFrame(task_stream)

    df_tasks["started_at_str"] = pd.to_datetime(df_tasks["started_at_str"])
    df_tasks["prev_task_time"] = df_tasks["started_at_str"].shift(periods=1)
    df_tasks["time_since_last_task"] = df_tasks["started_at_str"] - df_tasks["prev_task_time"]
    df_tasks["gap_seconds"] = df_tasks["time_since_last_task"].dt.total_seconds()
    print(df_tasks)
```

### My Output Verification:

```
       task_id      started_at_str      prev_task_time time_since_last_task  gap_seconds
    0      501 2026-07-18 08:00:00                 NaT                  NaT          NaN
    1      502 2026-07-18 08:05:30 2026-07-18 08:00:00      0 days 00:05:30        330.0
    2      503 2026-07-18 08:12:00 2026-07-18 08:05:30      0 days 00:06:30        390.0
    3      504 2026-07-18 08:30:15 2026-07-18 08:12:00      0 days 00:18:15       1095.0
```

---

## Challenge 6: The Partitioned Time Series Analytics Pipeline (The Final Boss)

**Scenario:** A messy distributed cluster database dumps unstructured telemetry shards. The data arrives split into separate horizontal files, containing formatting text spacing bugs, invalid system timestamp entries, administrative key tokens, and unsorted event orderings. You must build an end-to-end data refinery script component to stack raw shards, sanitize rows, cast strings, sort chronology tracks, calculate step latencies, build rolling averages, map running totals, and construct an independent dashboard pivot matrix.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    # Partition A: First sequence of messy telemetry blocks
    shard_a = {
        'event_id': [10002, 10001],
        'server_node': [' node_01 ', 'node_02 '],
        'timestamp_raw': ['2026-07-18 10:05:00', '2026-07-18 10:00:00'],
        'compute_cost': ['12.50', '8.00']
    }
    df_shard_a = pd.DataFrame(shard_a)

    # Partition B: Second sequence of messy telemetry blocks (Contains text noise & null fields)
    shard_b = {
        'event_id': [10003, 10004, 10005],
        'server_node': [' node_01', ' node_02 ', 'node_REJECT'],
        'timestamp_raw': ['2026-07-18 10:12:30', '2026-07-18 10:25:00', 'CORRUPT_DATA'],
        'compute_cost': ['15.20', '22.10', '9999.99']
    }
    df_shard_b = pd.DataFrame(shard_b)
```

### Your Tasks:

1. **Consolidate Distributed Input Shards:**
   * Vertically concatenate `df_shard_a` and `df_shard_b` into a unified master DataFrame named `df_raw_master`. Ensure raw duplicate index handles are fully suppressed.
2. **Apply Vector Cleanse and Inversion Masking:**
   * Discard rows where `server_node` matches the string `'REJECT'` using the **tilde (`~`) inversion mask operator**.
   * Strip out all formatting whitespaces from entries inside `server_node` and convert the strings entirely to **uppercase**.
3. **Execute Robust Chronological Parsing & Sorting:**
   * Transform `timestamp_raw` into true `datetime64[ns]` timestamp objects, forcing unparseable text errors to dissolve safely into `NaT`.
   * Purge rows containing missing structural timestamp tracking elements (`NaT`).
   * **Crucial step:** Time-series analytics rely on chronological order. Sort the entire DataFrame by your clean timestamp column in **ascending order** using `.sort_values()`, and reset the row index sequentially starting from `0`.
4. **Cast Numeric Features:**
   * Cast `compute_cost` explicitly into a standard float vector layout (`float64`).
5. **Isolate Latency Profiles & Step Differences:**
   * Use `.shift()` to calculate the timestamp difference between the current row and the previous row. Convert this full processing elapsed duration entirely into decimal **seconds** and save it to a column named `seconds_since_last_event`. Fill any resulting missing structural nulls (`NaN`) in the first row with `0.0`.
6. **Compile Advanced Window Aggregations (Target A):**
   * Using your sorted master DataFrame, add two new tracking columns to the grid:
     * **Column A1:** Calculate a **2-row rolling average** window of the `compute_cost` metric (set `min_periods=1`). Save it to a column named `rolling_avg_cost`.
     * **Column A2:** Calculate the continuous **cumulative running total sum** of `compute_cost` from the first row down to the last. Save it to a column named `cumulative_spend`.
   * Print this fully enriched master DataFrame table view.
7. **Reshape Dashboard Pivot Matrix (Target B):**
   * Using the exact same refined master DataFrame, construct a separate standalone pivoted matrix table view:
     * Map `event_id` values down the vertical index rows.
     * Shift unique `server_node` header strings horizontally to serve as header columns.
     * Map the numeric `compute_cost` values at the intersections, forcing any blank structural intersection elements to populate with `0.0` automatically.
     * Flatten the pivot matrix index layout structure cleanly, wipe out the floating axis name metadata parameter artifacts, and print the finished target B matrix.

### My Solution:

```python
    import pandas as pd

    # Partition A: First sequence of messy telemetry blocks
    shard_a = {
        "event_id": [10002, 10001],
        "server_node": [" node_01 ", "node_02 "],
        "timestamp_raw": ["2026-07-18 10:05:00", "2026-07-18 10:00:00"],
        "compute_cost": ["12.50", "8.00"],
    }
    df_shard_a = pd.DataFrame(shard_a)

    # Partition B: Second sequence of messy telemetry blocks (Contains text noise & null fields)
    shard_b = {
        "event_id": [10003, 10004, 10005],
        "server_node": [" node_01", " node_02 ", "node_REJECT"],
        "timestamp_raw": ["2026-07-18 10:12:30", "2026-07-18 10:25:00", "CORRUPT_DATA"],
        "compute_cost": ["15.20", "22.10", "9999.99"],
    }
    df_shard_b = pd.DataFrame(shard_b)

    df_raw_master = pd.concat([df_shard_a, df_shard_b], axis=0).reset_index(drop=1)
    mask = ~df_raw_master["server_node"].str.contains("REJECT")
    df_raw_master = df_raw_master[mask]

    df_raw_master["server_node"] = df_raw_master["server_node"].str.strip().str.upper()

    df_raw_master["timestamp_raw"] = pd.to_datetime(
        df_raw_master["timestamp_raw"], errors="coerce"
    )
    df_raw_master = df_raw_master.dropna(subset=["timestamp_raw"])
    df_raw_master = df_raw_master.sort_values(by="timestamp_raw").reset_index(drop=1)

    df_raw_master["compute_cost"] = df_raw_master["compute_cost"].astype("float")

    df_raw_master["prev_timestamp"] = df_raw_master["timestamp_raw"].shift(
        periods=1,
    )
    df_raw_master["time_difference"] = (
        df_raw_master["timestamp_raw"] - df_raw_master["prev_timestamp"]
    )
    df_raw_master["seconds_since_last_event"] = df_raw_master["time_difference"].dt.total_seconds()
    df_raw_master["seconds_since_last_event"] = df_raw_master["seconds_since_last_event"].fillna(
        0.0
    )
    print("Our Final DataFrame before moving to Targets\n", df_raw_master, "\n")

    df_raw_master = df_raw_master.drop(columns=["prev_timestamp", "time_difference"])
    df_raw_master["rolling_avg_cost"] = (
        df_raw_master["compute_cost"].rolling(window=2, min_periods=1).mean()
    )
    df_raw_master["cumulative_spend"] = df_raw_master["compute_cost"].cumsum()
    print("DataFrame after Target A:\n", df_raw_master, "\n")

    target_b = df_raw_master.pivot_table(
        index="event_id", columns="server_node", values="compute_cost", fill_value=0.0
    ).reset_index()
    target_b.columns.name = None
    print("Our Target B DataFrame:\n", target_b)
```

### My Output Verification:

```
    Our Final DataFrame before moving to Targets
       event_id server_node       timestamp_raw  compute_cost      prev_timestamp time_difference  seconds_since_last_event
    0     10001     NODE_02 2026-07-18 10:00:00           8.0                 NaT             NaT                       0.0
    1     10002     NODE_01 2026-07-18 10:05:00          12.5 2026-07-18 10:00:00 0 days 00:05:00                     300.0
    2     10003     NODE_01 2026-07-18 10:12:30          15.2 2026-07-18 10:05:00 0 days 00:07:30                     450.0
    3     10004     NODE_02 2026-07-18 10:25:00          22.1 2026-07-18 10:12:30 0 days 00:12:30                     750.0 

    DataFrame after Target A:
       event_id server_node       timestamp_raw  compute_cost  seconds_since_last_event  rolling_avg_cost  cumulative_spend
    0     10001     NODE_02 2026-07-18 10:00:00           8.0                       0.0              8.00               8.0
    1     10002     NODE_01 2026-07-18 10:05:00          12.5                     300.0             10.25              20.5
    2     10003     NODE_01 2026-07-18 10:12:30          15.2                     450.0             13.85              35.7
    3     10004     NODE_02 2026-07-18 10:25:00          22.1                     750.0             18.65              57.8 

    Our Target B DataFrame:
       event_id  NODE_01  NODE_02
    0     10001      0.0      8.0
    1     10002     12.5      0.0
    2     10003     15.2      0.0
    3     10004      0.0     22.1
```
