# Pandas Numerical Ranking and Window Ordering Practice Challenges

This document contains six progressive data engineering challenges designed to test basic vector ranking, custom tie-breaking methodologies, ascending and descending ranking directions, and multi-layered windowed partition ranking.

---

## Challenge 1: Log Priority Assignment (Basic Vector Ranking)

**Scenario:** A cloud optimization layer captures data engine resource allocations. Before provisioning more processing units, you must rank the execution runs globally by cost using dense sorting mechanics.

### Raw Data Input

```python
    import pandas as pd

    job_runs = {
        'run_id': [3001, 3002, 3003, 3004],
        'compute_cost_usd': [15.20, 89.50, 15.20, 42.00]
    }
    df_jobs = pd.DataFrame(job_runs)
```

### Your Tasks:

1. Use the `.rank()` method to evaluate the `compute_cost_usd` column globally from cheapest to most expensive (`ascending=True`).
2. Implement the `dense` tie-breaking methodology so that tied values receive identical rankings without leaving gaps in subsequent rank values.
3. Save the results into a column named `cost_rank` and print the updated DataFrame.

### My Solution:

```
    import pandas as pd

    job_runs = {
        "run_id": [3001, 3002, 3003, 3004],
        "compute_cost_usd": [15.20, 89.50, 15.20, 42.00],
    }
    df_jobs = pd.DataFrame(job_runs)

    df_jobs["cost_rank"] = df_jobs["compute_cost_usd"].rank(method="dense", ascending=True)
    print(df_jobs)
```

### My Output Verification:

```
       run_id  compute_cost_usd  cost_rank
    0    3001              15.2        1.0
    1    3002              89.5        3.0
    2    3003              15.2        1.0
    3    3004              42.0        2.0
```

---

## Challenge 2: Processing Latency Penalty (Descending Ranking)

**Scenario:** A queue worker registers execution delays across system processes. To target bottleneck operations for immediate debugging, you must rank the processing times in descending order.

### Raw Data Input

```python
    import pandas as pd

    queue_telemetry = {
        'task_uuid': ['TX-A', 'TX-B', 'TX-C', 'TX-D'],
        'delay_seconds': [4.5, 120.2, 4.5, 18.7]
    }
    df_queue = pd.DataFrame(queue_telemetry)
```

### Your Tasks:

1. Rank the `delay_seconds` values in descending order (`ascending=False`) so that the longest processing delay receives rank 1.
2. Apply the `dense` method to handle potential tie parameters.
3. Store the result in a column named `delay_severity_rank` and print the layout matrix.

### My Solution:

```
    import pandas as pd

    queue_telemetry = {
        "task_uuid": ["TX-A", "TX-B", "TX-C", "TX-D"],
        "delay_seconds": [4.5, 120.2, 4.5, 18.7],
    }
    df_queue = pd.DataFrame(queue_telemetry)

    df_queue["delay_severity_rank"] = df_queue["delay_seconds"].rank(
        method="dense", ascending=False
    )
    print(df_queue)
```

### My Output Verification:

```
      task_uuid  delay_seconds  delay_severity_rank
    0      TX-A            4.5                  3.0
    1      TX-B          120.2                  1.0
    2      TX-C            4.5                  3.0
    3      TX-D           18.7                  2.0
```

---

## Challenge 3: Streaming Deduplication Prep (The 'First' Tie-Breaker)

**Scenario:** A real-time streaming ingestion node captures duplicated events with identical timestamps. Before pulling specific unique records, you must assign an arbitrary sequential rank based purely on row appearance order to prevent mathematical rank grouping.

### Raw Data Input

```python
    import pandas as pd

    stream_logs = {
        'event_id': [7701, 7702, 7703, 7704],
        'timestamp_mark': ['10:00:05', '10:00:12', '10:00:05', '10:00:05']
    }
    df_stream = pd.DataFrame(stream_logs)
```

### Your Tasks:

1. Rank the `timestamp_mark` column vector globally in ascending order.
2. Configure the tie-breaking parameter argument to use `method='first'` to assign sequential numbers based on row position when timestamps match exactly.
3. Save the values into a column named `arrival_sequence_rank` and print the tracking data layout grid.

### My Solution:

```
    import pandas as pd

    stream_logs = {
        "event_id": [7701, 7702, 7703, 7704],
        "timestamp_mark": ["10:00:05", "10:00:12", "10:00:05", "10:00:05"],
    }
    df_stream = pd.DataFrame(stream_logs)

    df_stream["arrival_sequence_rank"] = df_stream["timestamp_mark"].rank(
        method="first", ascending=True
    )
    print(df_stream)
```

### My Output Verification:

```
       event_id timestamp_mark  arrival_sequence_rank
    0      7701       10:00:05                    1.0
    1      7702       10:00:12                    4.0
    2      7703       10:00:05                    2.0
    3      7704       10:00:05                    3.0
```

---

## Challenge 4: Partitioned Node Ranking (Windowed Group Ranking)

**Scenario:** A multi-region storage deployment logs transaction tracking traffic. Management needs to identify the highest workload spikes independently within each specific regional server partition rather than compiling a global report.

### Raw Data Input

```python
    import pandas as pd

    node_traffic = {
        'region_id': ['MUMBAI', 'DELHI', 'MUMBAI', 'DELHI', 'MUMBAI'],
        'node_token': ['N-1', 'N-2', 'N-3', 'N-4', 'N-5'],
        'requests_per_sec': [4500, 9200, 1200, 9200, 6800]
    }
    df_nodes = pd.DataFrame(node_traffic)
```

### Your Tasks:

1. Group your dataset by the `region_id` column.
2. Apply the `.rank()` function within the grouped partition windows to rank `requests_per_sec` in descending order. Use `method='dense'`.
3. Save this structural ranking layer to a column named `node_rank_in_region`. Print the table sorted by `region_id` and `node_rank_in_region`.

### My Solution:

```
    import pandas as pd

    node_traffic = {
        "region_id": ["MUMBAI", "DELHI", "MUMBAI", "DELHI", "MUMBAI"],
        "node_token": ["N-1", "N-2", "N-3", "N-4", "N-5"],
        "requests_per_sec": [4500, 9200, 1200, 9200, 6800],
    }
    df_nodes = pd.DataFrame(node_traffic)

    df_nodes["node_rank_in_region"] = df_nodes.groupby("region_id")["requests_per_sec"].rank(
        method="dense", ascending=False
    )
    df_nodes = df_nodes.sort_values(["region_id", "node_rank_in_region"])
    print(df_nodes)
```

### My Output Verification:

```
      region_id node_token  requests_per_sec  node_rank_in_region
    1     DELHI        N-2              9200                  1.0
    3     DELHI        N-4              9200                  1.0
    4    MUMBAI        N-5              6800                  1.0
    0    MUMBAI        N-1              4500                  2.0
    2    MUMBAI        N-3              1200                  3.0
```

---

## Challenge 5: Chrono-Ingestion Penalty Audit (Grouped Time Rankings)

**Scenario:** An automated script schedules diagnostic test suites. To spot execution runtime regressions over time, you must track down the rank ordering of testing durations within individual environment spaces.

### Raw Data Input

```python
    import pandas as pd

    test_matrix = {
        'env_space': ['STAGING', 'PROD', 'PROD', 'STAGING', 'STAGING'],
        'test_name': ['Suite-A', 'Suite-B', 'Suite-C', 'Suite-D', 'Suite-E'],
        'duration_ms': [450, 1200, 850, 450, 990]
    }
    df_tests = pd.DataFrame(test_matrix)
```

### Your Tasks:

1. Partition the data tracking grid by `env_space`.
2. Rank the `duration_ms` column inside each group in ascending order using the `min` tie-breaking methodology (where tied values all receive the lowest rank of that tie group).
3. Save the numeric rank array into a column named `test_duration_rank` and print the updated structural summary dataframe layout.

### My Solution:

```
    import pandas as pd

    test_matrix = {
        "env_space": ["STAGING", "PROD", "PROD", "STAGING", "STAGING"],
        "test_name": ["Suite-A", "Suite-B", "Suite-C", "Suite-D", "Suite-E"],
        "duration_ms": [450, 1200, 850, 450, 990],
    }
    df_tests = pd.DataFrame(test_matrix)

    df_tests["test_duration_rank"] = df_tests.groupby("env_space")["duration_ms"].rank(
        method="min", ascending=True
    )
    print(df_tests)
```

### My Output Verification:

```
      env_space test_name  duration_ms  test_duration_rank
    0   STAGING   Suite-A          450                 1.0
    1      PROD   Suite-B         1200                 2.0
    2      PROD   Suite-C          850                 1.0
    3   STAGING   Suite-D          450                 1.0
    4   STAGING   Suite-E          990                 3.0
```

---

## Challenge 6: The Partitioned Telemetry Pipeline Refinery (The Final Boss)

**Scenario:** A messy operational data lake landing pool receives unstructured text files recording hardware log shards. The data arrives split across separate chunks, containing trailing whitespace errors, corrupted text fields, administrative key leaks, and scrambled temporal orderings. You must construct an optimized data engineering production component to stack raw shards, sanitize strings, parse types, sort timelines, calculate step deltas, compute partitioned rolling windows, execute grouped dense ranking, apply memorycategory tuning optimizations, reshape layout matrices, and enforce target aggregates.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    # Data Shard 1: Initial payload chunk tracking telemetry data
    shard_1 = {
        'tx_id': [88002, 88001],
        'cluster_node': [' srv_mumbai ', 'srv_delhi '],
        'timestamp_raw': ['2026-07-20 11:05:00', '2026-07-20 11:00:00'],
        'compute_cost': ['45.20', '12.50']
    }
    df_s1 = pd.DataFrame(shard_1)

    # Data Shard 2: Second payload chunk tracking telemetry data (Contains string noise)
    shard_2 = {
        'tx_id': [88003, 88004, 88005],
        'cluster_node': [' srv_mumbai', ' srv_delhi ', 'srv_REJECT'],
        'timestamp_raw': ['2026-07-20 11:12:30', '2026-07-20 11:25:00', 'BAD_LOG_TIME'],
        'compute_cost': ['88.40', '12.50', '9999.00']
    }
    df_s2 = pd.DataFrame(shard_2)
```

### Your Tasks:

1. **Consolidate Distributed Staging Shards:**
   * Vertically concatenate `df_s1` and `df_s2` into a single master working structure named `df_raw_master`. Suppress row index accumulation artifacts.
2. **Apply Vector Cleanse and Inversion Masking:**
   * Discard rows where `cluster_node` matches the string `'REJECT'` using the **tilde (`~`) inversion mask operator**.
   * Strip away formatting whitespaces from entries inside `cluster_node` and force the characters to **uppercase**.
3. **Execute Robust Chronological Parsing & Sorting:**
   * Transform `timestamp_raw` into true `datetime64[ns]` timestamp objects, forcing unparseable text errors to dissolve safely into `NaT`.
   * Purge rows containing missing structural timestamp tracking elements (`NaT`) immediately *after* the type casting pass.
   * Sort the entire DataFrame chronologically by `timestamp_raw` in **ascending order** and reset the row index sequentially starting from `0`.
4. **Cast Numeric Features:**
   * Cast the `compute_cost` column explicitly into a standard float vector layout (`float64`).
5. **Isolate Grouped Step Latencies & Advanced Ranking:**
   * Group your sorted master DataFrame by the clean `cluster_node` column and perform two window operations:
     * **Operation 1:** Calculate the timestamp difference between the current row and the previous row *within each cluster partition*. Convert this elapsed duration entirely into decimal **seconds** and save it to a column named `secs_since_last_node_event`. Fill any resulting missing null parameters (`NaN`) with `0.0`.
     * **Operation 2:** Rank the `compute_cost` metrics *within each cluster partition* in descending order (highest cost gets rank 1). Use the **`dense`** tie-breaking method. Save this calculation vector into a column named `cost_rank_in_node`.
6. **Optimize Storage footprint Structure:**
   * Convert the `cluster_node` column explicitly to the **`category`** datatype to ensure optimal processing memory footprint sizes.
   * Print the fully enriched master DataFrame.
7. **Reshape Dashboard Pivot Matrix (Target B):**
   * Using your refined master DataFrame, construct a separate standalone pivoted dashboard matrix structure table.
   * Map `tx_id` values down the vertical index rows.
   * Shift unique `cluster_node` structural strings horizontally to serve as header columns.
   * Map the numeric `compute_cost` values at the intersections, forcing any empty structural cells to populate with `0.0` automatically. Enforce `aggfunc='sum'` to safeguard data totals, clear out the floating axis name metadata parameter artifacts, and print the finished pivot matrix table.

### My Solution:

```
import pandas as pd

# Data Shard 1: Initial payload chunk tracking telemetry data
shard_1 = {
    "tx_id": [88002, 88001],
    "cluster_node": [" srv_mumbai ", "srv_delhi "],
    "timestamp_raw": ["2026-07-20 11:05:00", "2026-07-20 11:00:00"],
    "compute_cost": ["45.20", "12.50"],
}
df_s1 = pd.DataFrame(shard_1)
# Data Shard 2: Second payload chunk tracking telemetry data (Contains string noise)
shard_2 = {
    "tx_id": [88003, 88004, 88005],
    "cluster_node": [" srv_mumbai", " srv_delhi ", "srv_REJECT"],
    "timestamp_raw": ["2026-07-20 11:12:30", "2026-07-20 11:25:00", "BAD_LOG_TIME"],
    "compute_cost": ["88.40", "12.50", "9999.00"],
}
df_s2 = pd.DataFrame(shard_2)

df_raw_master = pd.concat([df_s1, df_s2]).reset_index(drop=True)
mask = ~df_raw_master["cluster_node"].str.contains("REJECT")
df_raw_master = df_raw_master[mask]
df_raw_master["cluster_node"] = df_raw_master["cluster_node"].str.strip().str.upper()
df_raw_master["timestamp_raw"] = pd.to_datetime(
    df_raw_master["timestamp_raw"], errors="coerce"
)
df_raw_master = df_raw_master.dropna(subset=["timestamp_raw"])
df_raw_master = df_raw_master.sort_values("timestamp_raw").reset_index(drop=True)
df_raw_master["compute_cost"] = df_raw_master["compute_cost"].astype("float")

# Operation 1
df_raw_master["secs_since_last_node_event"] = df_raw_master[
    "timestamp_raw"
] - df_raw_master.groupby("cluster_node")["timestamp_raw"].shift(periods=1)
df_raw_master["secs_since_last_node_event"] = df_raw_master[
    "secs_since_last_node_event"
].dt.total_seconds()
df_raw_master["secs_since_last_node_event"] = df_raw_master[
    "secs_since_last_node_event"
].fillna(0)

# Operation 2
df_raw_master["cost_rank_in_node"] = df_raw_master.groupby("cluster_node")[
    "compute_cost"
].rank(method="dense", ascending=False)

df_raw_master["cluster_node"] = df_raw_master["cluster_node"].astype("category")
print(df_raw_master, "\n")

target_b = df_raw_master.pivot_table(
    index="tx_id",
    columns="cluster_node",
    values="compute_cost",
    fill_value=0.0,
    aggfunc="sum",
    observed=True,
).reset_index()
target_b.columns.name = None
print(target_b)
```

### My Output Verification:

```
       tx_id cluster_node       timestamp_raw  compute_cost  secs_since_last_node_event  cost_rank_in_node
    0  88001    SRV_DELHI 2026-07-20 11:00:00          12.5                         0.0                1.0
    1  88002   SRV_MUMBAI 2026-07-20 11:05:00          45.2                         0.0                2.0
    2  88003   SRV_MUMBAI 2026-07-20 11:12:30          88.4                       450.0                1.0
    3  88004    SRV_DELHI 2026-07-20 11:25:00          12.5                      1500.0                1.0 

       tx_id  SRV_DELHI  SRV_MUMBAI
    0  88001       12.5         0.0
    1  88002        0.0        45.2
    2  88003        0.0        88.4
    3  88004       12.5         0.0
```
