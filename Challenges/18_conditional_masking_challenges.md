# Pandas Conditional Element Masking and Clipping Practice Challenges

This document contains six progressive data engineering challenges designed to test native boolean value preservation (`.where()`), target row masking (`.mask()`), numerical outlier truncation (`.clip()`), and end-to-end multi-source data pipeline validation.

---

## Challenge 1: Preserving Compliant Telemetry (Basic `.where()`)

**Scenario:** An edge compute monitoring layer captures device temperature readings. Values above 85°C indicate an immediate thermal breach. You must keep all valid, compliant readings intact while substituting any breaches with a fallback value of `NaN`.

### Raw Data Input

```python
    import pandas as pd

    temp_data = {
        'sensor_id': ['S1', 'S2', 'S3', 'S4'],
        'temp_celsius': [42.1, 88.5, 71.0, 95.2]
    }
    df_temp = pd.DataFrame(temp_data)
```

### Your Tasks:

1. Use the `.where()` method to evaluate `temp_celsius`. Keep values where the condition `temp_celsius <= 85.0` is `True`.
2. Save the output to a column named `compliant_temp` and print the DataFrame.

### My Solution:

```python
    import pandas as pd

    temp_data = {"sensor_id": ["S1", "S2", "S3", "S4"], "temp_celsius": [42.1, 88.5, 71.0, 95.2]}
    df_temp = pd.DataFrame(temp_data)

    df_temp["compliant_temp"] = df_temp["temp_celsius"].where(df_temp["temp_celsius"] <= 85.0)
    print(df_temp)
```

### My Output Verification:

```
      sensor_id  temp_celsius compliant_temp
    0        S1          42.1           42.1
    1        S2          88.5            NaN
    2        S3          71.0           71.0
    3        S4          95.2            NaN
```

---

## Challenge 2: Masking Anomaly Spikes (Basic `.mask()`)

**Scenario:** A queue coordinator logs worker delay durations. Spikes over 100 seconds represent network dropouts. You must use `.mask()` to replace those specific anomaly spikes with a flag value of `-1.0` without altering the safe queue records.

### Raw Data Input

```python
    import pandas as pd

    queue_data = {
        'worker_id': ['W-101', 'W-102', 'W-103', 'W-104'],
        'latency_sec': [12.4, 450.0, 8.1, 120.5]
    }
    df_queue = pd.DataFrame(queue_data)
```

### Your Tasks:

1. Use `.mask()` to evaluate `latency_sec`. Target rows where `latency_sec > 100.0` and replace those values with `-1.0`.
2. Save the output into a column named `sanitized_latency` and print the updated DataFrame.

### My Solution:

```python
    import pandas as pd

    queue_data = {
        "worker_id": ["W-101", "W-102", "W-103", "W-104"],
        "latency_sec": [12.4, 450.0, 8.1, 120.5],
    }
    df_queue = pd.DataFrame(queue_data)

    df_queue["sanitized_latency"] = df_queue["latency_sec"].mask(
        df_queue["latency_sec"] > 100.0, -1.0
    )
    print(df_queue)
```

### My Output Verification:

```
      worker_id  latency_sec  sanitized_latency
    0     W-101         12.4               12.4
    1     W-102        450.0               -1.0
    2     W-103          8.1                8.1
    3     W-104        120.5               -1.0
```

---

## Challenge 3: Hard Boundary Truncation (Basic `.clip()`)

**Scenario:** A cloud billing estimator calculates node usage percentages. Due to timing jitter, raw calculation logs occasionally output erroneous values like `-5.0%` or `112.0%`. You must force all values to lie strictly within standard physical boundaries (0.0% to 100.0%).

### Raw Data Input

```python
    import pandas as pd

    billing_data = {
        'cluster_id': ['C-01', 'C-02', 'C-03', 'C-04'],
        'usage_pct': [-5.2, 45.0, 112.5, 88.0]
    }
    df_billing = pd.DataFrame(billing_data)
```

### Your Tasks:

1. Use `.clip()` on `usage_pct` to set a strict lower boundary floor of `0.0` and an upper ceiling of `100.0`.
2. Save the resulting vector into a column named `bounded_usage_pct` and print the DataFrame.

### My Solution:

```python
    import pandas as pd

    billing_data = {
        "cluster_id": ["C-01", "C-02", "C-03", "C-04"],
        "usage_pct": [-5.2, 45.0, 112.5, 88.0],
    }
    df_billing = pd.DataFrame(billing_data)

    df_billing["bounded_usage_pct"] = df_billing["usage_pct"].clip(0.0, 100.0)
    print(df_billing)
```

### My Output Verification:

```
      cluster_id  usage_pct  bounded_usage_pct
    0       C-01       -5.2                0.0
    1       C-02       45.0               45.0
    2       C-03      112.5              100.0
    3       C-04       88.0               88.0
```

---

## Challenge 4: Multi-Threshold Masking (Chained `.mask()`)

**Scenario:** A data privacy pipeline cleans user analytics telemetry before writing to a long-term data warehouse. Any invalid user ID numbers (`< 0`) or corrupted status codes (`>= 900`) must be masked out and set to `None` / `NaN`.

### Raw Data Input

```python
    import pandas as pd

    privacy_logs = {
        'record_id': [1, 2, 3, 4],
        'user_id': [8801, -999, 4402, 1205],
        'status_code': [200, 200, 950, 404]
    }
    df_privacy = pd.DataFrame(privacy_logs)
```

### Your Tasks:

1. Use `.mask()` on `user_id` to replace any values `< 0` with `None`.
2. Use `.mask()` on `status_code` to replace any values `>= 900` with `None`.
3. Print the sanitized DataFrame.

### My Solution:

```python
    import pandas as pd

    privacy_logs = {
        "record_id": [1, 2, 3, 4],
        "user_id": [8801, -999, 4402, 1205],
        "status_code": [200, 200, 950, 404],
    }
    df_privacy = pd.DataFrame(privacy_logs)

    df_privacy["user_id"] = df_privacy["user_id"].mask(df_privacy["user_id"] < 0, "None")
    df_privacy["status_code"] = df_privacy["status_code"].mask(df_privacy["status_code"] >= 900)
    print(df_privacy)
```

### My Output Verification:

```
       record_id user_id status_code
    0          1    8801         200
    1          2    None         200
    2          3    4402        None
    3          4    1205         404
```

---

## Challenge 5: Dynamic Window Clipping (Grouped Metric Ceilings)

**Scenario:** To prevent runaway server bills, management sets an operational cap on compute query costs. Any single query cost that exceeds twice the average cost of its server tier must be clipped down to that tier's 2x threshold.

### Raw Data Input

```python
    import pandas as pd

    tier_telemetry = {
        'tier': ['BASIC', 'BASIC', 'PREMIUM', 'PREMIUM'],
        'query_id': ['Q1', 'Q2', 'Q3', 'Q4'],
        'cost_usd': [10.0, 50.0, 100.0, 150.0]
    }
    df_tier = pd.DataFrame(tier_telemetry)
```

### Your Tasks:

1. Calculate the mean `cost_usd` grouped by `tier`.
2. Multiply that mean vector by `2.0` to define a `max_allowed_cost` threshold column.
3. Use `.clip()` or `.where()` to cap `cost_usd` so it never exceeds `max_allowed_cost`.
4. Save the result to `capped_cost_usd` and print the updated table.

### My Solution:

```python
    import pandas as pd

    tier_telemetry = {
        "tier": ["BASIC", "BASIC", "PREMIUM", "PREMIUM"],
        "query_id": ["Q1", "Q2", "Q3", "Q4"],
        "cost_usd": [10.0, 50.0, 100.0, 150.0],
    }
    df_tier = pd.DataFrame(tier_telemetry)

    mean_cost = df_tier.groupby("tier")["cost_usd"].transform("mean")
    df_tier["max_allowed_cost"] = mean_cost * 2.0

    df_tier["capped_cost_usd"] = df_tier["cost_usd"].clip(upper=df_tier["max_allowed_cost"])
    print(df_tier)
```

### My Output Verification:

```
          tier query_id  cost_usd  max_allowed_cost  capped_cost_usd
    0    BASIC       Q1      10.0              60.0             10.0
    1    BASIC       Q2      50.0              60.0             50.0
    2  PREMIUM       Q3     100.0             250.0            100.0
    3  PREMIUM       Q4     150.0             250.0            150.0
```

---

## Challenge 6: The Operational Safeguard & Anomaly Refinery Pipeline (The Final Boss)

**Scenario:** A high-frequency telemetry pipeline ingests chaotic metrics across distributed regional shards. The datasets contain text formatting bugs, invalid timestamps, unparsed cost metrics, runaway metric spikes, and corrupt diagnostic status codes. You must construct a complete production data engineering script to stack raw shards, sanitize text fields, parse timestamps, calculate isolated windowed step delays, apply conditional masking on error codes, enforce strict numerical outlier clipping boundaries, cast memory types, build grouped summaries, and structure an independent pivot matrix.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    # Partition A: First sequence of telemetry metrics
    shard_a = {
        'tx_id': [55002, 55001],
        'region_tag': [' mumbai_srv ', 'delhi_srv '],
        'timestamp_raw': ['2026-07-21 08:05:00', '2026-07-21 08:00:00'],
        'latency_ms': [-15.0, 120.0],
        'status_code': [200, 500]
    }
    df_shard_a = pd.DataFrame(shard_a)

    # Partition B: Second sequence of telemetry metrics (Contains text noise & bad codes)
    shard_b = {
        'tx_id': [55003, 55004, 55005],
        'region_tag': [' mumbai_srv', ' delhi_srv ', 'reject_srv'],
        'timestamp_raw': ['2026-07-21 08:12:30', '2026-07-21 08:25:00', 'BAD_TIMESTAMP'],
        'latency_ms': [450.0, 1250.0, 9999.0],
        'status_code': [200, 999, 500]
    }
    df_shard_b = pd.DataFrame(shard_b)
```

### Your Tasks:

1. **Consolidate Distributed Staging Input Shards:**
   * Vertically concatenate `df_shard_a` and `df_shard_b` into a single working DataFrame named `df_raw_master`. Ensure raw duplicate index handles are fully suppressed.
2. **Apply Vector Cleanse and Inversion Masking:**
   * Discard rows where `region_tag` contains `'REJECT'` (case-insensitive) using the **tilde (`~`) inversion mask operator**.
   * Strip away all formatting whitespaces from entries inside `region_tag` and force the characters to **uppercase**.
3. **Execute Robust Chronological Parsing & Sorting:**
   * Transform `timestamp_raw` into true `datetime64[ns]` timestamp objects, forcing unparseable text errors to dissolve safely into `NaT`.
   * Purge rows containing missing structural timestamp tracking elements (`NaT`) immediately *after* the type casting pass.
   * Sort the entire DataFrame chronologically by `timestamp_raw` in **ascending order** and reset the row index sequentially starting from `0`.
4. **Enforce Hard Metric Boundaries (`.clip()`):**
   * The `latency_ms` column contains invalid negative readings (`-15.0`) and extreme runaway spikes (`1250.0`).
   * Apply `.clip()` to cap `latency_ms` strictly between a lower floor of `0.0` and an upper ceiling of `500.0`.
5. **Mask Corrupted Operational Codes (`.mask()`):**
   * Operational status codes `>= 900` represent corrupted router dumps.
   * Use `.mask()` on `status_code` to replace any value `>= 900` with `-1`.
6. **Isolate Grouped Step Latencies (`.groupby().shift()`):**
   * Group your sorted DataFrame by `region_tag`.
   * Calculate the timestamp difference between the current row and the previous row **within each region partition**.
   * Convert this elapsed duration entirely into decimal **seconds** and save it to a column named `secs_since_last_region_event`. Fill any resulting missing null parameters (`NaN`) with `0.0`.
7. **Optimize Memory & Reshape Outputs:**
   * Convert `region_tag` explicitly to the **`category`** datatype.
   * **Target A (Summary Metrics):** Group by `region_tag` (pass `observed=True`). Aggregate `latency_ms` to calculate `avg_latency_ms` (mean) and `total_requests` (count). Reset the index.
   * **Target B (Pivot Matrix):** Pivot the DataFrame setting `index='tx_id'`, `columns='region_tag'`, `values='latency_ms'`, `fill_value=0.0`, and `aggfunc='sum'`. Flatten the index and clear `columns.name`.
   * Print both Target A and Target B processed matrices.

### My Solution:

```python
    import pandas as pd

    # Partition A: First sequence of telemetry metrics
    shard_a = {
        "tx_id": [55002, 55001],
        "region_tag": [" mumbai_srv ", "delhi_srv "],
        "timestamp_raw": ["2026-07-21 08:05:00", "2026-07-21 08:00:00"],
        "latency_ms": [-15.0, 120.0],
        "status_code": [200, 500],
    }
    df_shard_a = pd.DataFrame(shard_a)
    # Partition B: Second sequence of telemetry metrics (Contains text noise & bad codes)
    shard_b = {
        "tx_id": [55003, 55004, 55005],
        "region_tag": [" mumbai_srv", " delhi_srv ", "reject_srv"],
        "timestamp_raw": ["2026-07-21 08:12:30", "2026-07-21 08:25:00", "BAD_TIMESTAMP"],
        "latency_ms": [450.0, 1250.0, 9999.0],
        "status_code": [200, 999, 500],
    }
    df_shard_b = pd.DataFrame(shard_b)

    df_raw_master = pd.concat([df_shard_a, df_shard_b]).reset_index(drop=True)
    mask = ~df_raw_master["region_tag"].str.upper().str.contains("REJECT")
    df_raw_master = df_raw_master[mask]
    df_raw_master["region_tag"] = df_raw_master["region_tag"].str.strip().str.upper()

    df_raw_master["timestamp_raw"] = pd.to_datetime(
        df_raw_master["timestamp_raw"], errors="coerce"
    )
    df_raw_master = df_raw_master.dropna(subset=["timestamp_raw"])
    df_raw_master = df_raw_master.sort_values(by="timestamp_raw", ascending=True).reset_index(
        drop=True
    )

    df_raw_master["latency_ms"] = df_raw_master["latency_ms"].clip(0.0, 500.0)
    df_raw_master["status_code"] = df_raw_master["status_code"].mask(
        df_raw_master["status_code"] >= 900, -1
    )

    df_raw_master["secs_since_last_region_event"] = df_raw_master[
        "timestamp_raw"
    ] - df_raw_master.groupby("region_tag")["timestamp_raw"].shift(periods=1)

    df_raw_master["secs_since_last_region_event"] = df_raw_master[
        "secs_since_last_region_event"
    ].dt.total_seconds()
    df_raw_master["secs_since_last_region_event"] = df_raw_master[
        "secs_since_last_region_event"
    ].fillna(0)

    df_raw_master["region_tag"] = df_raw_master["region_tag"].astype("category")
    print(df_raw_master, "\n")

    target_a = (
        df_raw_master.groupby("region_tag", observed=True)
        .agg(avg_latency_ms=("latency_ms", "mean"), total_requests=("tx_id", "count"))
        .reset_index()
    )
    print(target_a, "\n")

    target_b = df_raw_master.pivot_table(
        index="tx_id",
        columns="region_tag",
        values="latency_ms",
        fill_value=0.0,
        aggfunc="sum",
        observed=True,
    ).reset_index()
    target_b.columns.name = None
    print(target_b)
```

### My Output Verification:

```
       tx_id  region_tag       timestamp_raw  latency_ms  status_code  secs_since_last_region_event
    0  55001   DELHI_SRV 2026-07-21 08:00:00       120.0          500                           0.0
    1  55002  MUMBAI_SRV 2026-07-21 08:05:00         0.0          200                           0.0
    2  55003  MUMBAI_SRV 2026-07-21 08:12:30       450.0          200                         450.0
    3  55004   DELHI_SRV 2026-07-21 08:25:00       500.0           -1                        1500.0 

       region_tag  avg_latency_ms  total_requests
    0   DELHI_SRV           310.0               2
    1  MUMBAI_SRV           225.0               2 

       tx_id  DELHI_SRV  MUMBAI_SRV
    0  55001      120.0         0.0
    1  55002        0.0         0.0
    2  55003        0.0       450.0
    3  55004      500.0         0.0
```
