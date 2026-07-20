# Pandas Performance Optimization and Memory Tuning Practice Challenges

This document contains six progressive data engineering challenges designed to test row-by-row performance auditing, high-speed vector condition mapping, low-cardinality memory compression, and end-to-end multi-source big data integration profiling.

---

## Challenge 1: Auditing the Row Loop Bottleneck (Basic `.apply()` Profiling)

**Scenario:** A staging ingestion script evaluates error logs using a legacy row-by-row processing framework. Before refactoring the module, you must implement a prototype using `.apply()` to establish a runtime baseline for comparison.

### Raw Data Input

```python
    import pandas as pd

    log_dump = {
        'log_id': [77001, 77002, 77003],
        'response_code': [200, 500, 404]
    }
    df_logs = pd.DataFrame(log_dump)
```

### Your Tasks:

1. Define a standard Python function named `classify_code(code)` that returns `'ERROR'` if the code is greater than or equal to `400`, and `'SUCCESS'` otherwise.
2. Use the `.apply()` method on the `response_code` column to map each individual row cell through your function.
3. Save the result to a column named `status_apply` and print the DataFrame.

### My Solution:

```
    import pandas as pd

    log_dump = {"log_id": [77001, 77002, 77003], "response_code": [200, 500, 404]}
    df_logs = pd.DataFrame(log_dump)


    def classify_code(code):
        if code >= 400:
            return "ERROR"
        else:
            return "SUCCESS"


    df_logs["status_apply"] = df_logs["response_code"].apply(classify_code)
    print(df_logs)
```

### My Output Verification:

```
       log_id  response_code status_apply
    0   77001            200      SUCCESS
    1   77002            500        ERROR
    2   77003            404        ERROR
```

---

## Challenge 2: Blazing-Fast Vector Mapping (Basic `np.where()`)

**Scenario:** To optimize the pipeline built in Challenge 1 for larger production datasets, you must eliminate the row-by-row interpreter overhead completely using native vectorized operations.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    log_dump = {
        'log_id': [77001, 77002, 77003],
        'response_code': [200, 500, 404]
    }
    df_logs = pd.DataFrame(log_dump)
```

### Your Tasks:

1. Use `np.where()` to evaluate the `response_code` column concurrently at the C-layer without using a Python loop.
2. If `response_code >= 400`, map it to `'ERROR'`; otherwise, map it to `'SUCCESS'`.
3. Save the result to a column named `status_vector` and print the DataFrame.

### My Solution:

```
    import pandas as pd
    import numpy as np

    log_dump = {"log_id": [77001, 77002, 77003], "response_code": [200, 500, 404]}
    df_logs = pd.DataFrame(log_dump)

    df_logs["status_vector"] = np.where(df_logs["response_code"] >= 400, "ERROR", "SUCCESS")
    print(df_logs)
```

### My Output Verification:

```
       log_id  response_code status_vector
    0   77001            200       SUCCESS
    1   77002            500         ERROR
    2   77003            404         ERROR
```

---

## Challenge 3: Taming Nested Conditions (`np.where()` Nesting)

**Scenario:** The infrastructure team requests a more granular categorization vector. Instead of a binary state flag, you need to map response codes across three distinct operational tiers concurrently without using loops or `.apply()`.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    server_dump = {
        'request_id': [101, 102, 103, 104],
        'http_status': [200, 503, 301, 401]
    }
    df_server = pd.DataFrame(server_dump)
```

### Your Tasks:

1. Use nested `np.where()` statements to evaluate the entire `http_status` vector simultaneously.
2. Implement the following structural rules:
   * If `http_status >= 500`, map to `'SERVER_FAULT'`.
   * Else if `http_status >= 400`, map to `'CLIENT_FAULT'`.
   * Otherwise, map to `'REDIRECT_OR_SUCCESS'`.
3. Save the outcome array to `fault_category` and print the DataFrame.

### My Solution:

```
    import pandas as pd
    import numpy as np

    server_dump = {"request_id": [101, 102, 103, 104], "http_status": [200, 503, 301, 401]}
    df_server = pd.DataFrame(server_dump)

    df_server["fault_category"] = np.where(
        df_server["http_status"] >= 400,
        np.where(df_server["http_status"] >= 500, "SERVER_FAULT", "CLIENT_FAULT"),
        "REDIRECT_OR_SUCCESS",
    )
    print(df_server)
```

### My Output Verification:

```
       request_id  http_status       fault_category
    0         101          200  REDIRECT_OR_SUCCESS
    1         102          503         SERVER_FAULT
    2         103          301  REDIRECT_OR_SUCCESS
    3         104          401         CLIENT_FAULT
```

---

## Challenge 4: Memory footprint Diagnostics (Evaluating `.memory_usage()`)

**Scenario:** A large staging table contains environment deployment labels stored as standard object text pointers. You must analyze the exact byte footprint of the column before optimizing its structure.

### Raw Data Input

```python
    import pandas as pd

    # Repetitive structural strings representing server environment spaces
    env_data = {'env_tag': ['PRODUCTION', 'DEVELOPMENT', 'STAGING', 'PRODUCTION'] * 500}
    df_env = pd.DataFrame(env_data)
```

### Your Tasks:

1. Use the `.memory_usage(deep=True)` method on `df_env` to extract the exact total allocation footprint in bytes for the `env_tag` column.
2. Print this number to the console terminal layout layer.

### My Solution:

```
    import pandas as pd

    # Repetitive structural strings representing server environment spaces
    env_data = {"env_tag": ["PRODUCTION", "DEVELOPMENT", "STAGING", "PRODUCTION"] * 500}
    df_env = pd.DataFrame(env_data)

    print("Memory Usage:", df_env.memory_usage(deep=True)["env_tag"], "bytes")
```

### My Output Verification:

```
    Memory Usage: 117000 bytes
```

---

## Challenge 5: Low-Cardinality Memory Compression (Casting to `category`)

**Scenario:** Using the exact same dataset from Challenge 4, you must apply categorical data compression to shrink the RAM usage footprint of the highly repetitive environment labels.

### Raw Data Input

```python
    import pandas as pd

    env_data = {'env_tag': ['PRODUCTION', 'DEVELOPMENT', 'STAGING', 'PRODUCTION'] * 500}
    df_env = pd.DataFrame(env_data)
```

### Your Tasks:

1. Cast the `env_tag` column explicitly from its default object structure into the memory-efficient `category` datatype using `.astype()`.
2. Check the updated column byte allocation using `.memory_usage(deep=True)`.
3. Print the new byte size alongside the old byte size to confirm the optimization drop.

### My Solution:

```
    import pandas as pd

    env_data = {"env_tag": ["PRODUCTION", "DEVELOPMENT", "STAGING", "PRODUCTION"] * 500}
    df_env = pd.DataFrame(env_data)

    print("Memory Usage Before:", df_env.memory_usage(deep=True)["env_tag"], "bytes")
    df_env["env_tag"] = df_env["env_tag"].astype("category")
    print("Memory Usage After:", df_env.memory_usage(deep=True)["env_tag"], "bytes")
```

### My Output Verification:

```
    Memory Usage Before: 117000 bytes
    Memory Usage After: 2283 bytes
```

---

## Challenge 6: The Distributed Multi-Source Integration Pipeline (The Final Boss)

**Scenario:** A production analytics bucket drops massive horizontal text shards tracking raw telemetry metrics. The datasets arrive partitioned, containing trailing whitespace formatting errors, invalid numeric parameters, and highly redundant text flags. You must implement an optimized data engineering script component to stack raw components, sanitize strings, parse datetimes safely, filter out system noise, cast types, execute high-speed vector condition mapping, compress low-cardinality tags, and compute aggregated summaries.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    # Component Shard 1: First split vertical partition of messy analytics tracking data
    shard_1 = {
        'tx_id': [99001, 99002],
        'node_code': [' srv_mumbai ', 'srv_delhi '],
        'latency_str': ['45.2', '120.8'],
        'team_tag': ['DATA_PLATFORM', 'CORE_INFRA']
    }
    df_shard_1 = pd.DataFrame(shard_1)

    # Component Shard 2: Second split vertical partition (Contains anomalies & text noise)
    shard_2 = {
        'tx_id': [99003, 99004, 99005],
        'node_code': [' srv_mumbai', ' srv_noida ', 'srv_REJECT'],
        'latency_str': ['88.4', '12.1', '9999.9'],
        'team_tag': ['DATA_PLATFORM', 'SECURITY_OPS', 'CORE_INFRA']
    }
    df_shard_2 = pd.DataFrame(shard_2)
```

### Your Tasks:

1. **Consolidate Distributed Shard Inputs:**
   * Vertically concatenate `df_shard_1` and `df_shard_2` into a unified master structure named `df_raw_master`. Ensure raw duplicate index tracking labels are suppressed.
2. **Apply Vector Cleanse and Inversion Masking:**
   * Drop rows where `node_code` contains the text string `'REJECT'` using the **tilde (`~`) inversion mask operator**.
   * Strip out all formatting whitespaces from entries inside the `node_code` column and force the entire string layer into **uppercase**.
3. **Robust Numeric Parsing:**
   * Cast the `latency_str` column explicitly into a standard float vector layout (`float64`).
4. **Implement High-Speed Conditional Vector Mapping:**
   * To avoid the processing bottlenecks of a row loop function (`.apply()`), use **`np.where()`** to evaluate the entire `latency_str` vector simultaneously.
   * If `latency_str > 100.0`, map the coordinate to `'HIGH_LATENCY'`; otherwise, map it to `'NORMAL_LATENCY'`. Save this array to a column named `performance_tier`.
5. **Optimize Storage footprint Structure:**
   * Review the columns. Both `node_code` and `team_tag` contain highly repetitive text values. Convert both columns explicitly to the **`category`** datatype to reduce RAM consumption.
6. **Compile Aggregated Metric Analytics (Target A):**
   * Group your optimized master DataFrame by the `team_tag` column.
   * Apply the `.agg()` tool window to calculate the **mean** average value of `latency_str` (rename to `avg_latency`) and the absolute **count** of entries (rename to `total_tx`). Reset the index tracks cleanly so your grouping field labels are fully preserved as a column.
7. **Reshape Dashboard Pivot Matrix (Target B):**
   * Using the exact same refined master DataFrame *prior* to your Target A aggregation step, construct a separate standalone pivoted dashboard matrix.
   * Map `tx_id` values down the vertical index rows.
   * Shift unique `node_code` structural strings horizontally to serve as header columns.
   * Map the numeric `latency_str` values at the intersections, forcing any empty structural cells to populate with `0.0` automatically.
   * Flatten the pivot matrix index layout structure cleanly, wipe out the floating axis name metadata parameter artifacts, and print both Target A and Target B processed matrices.

### My Solution:

```
    import pandas as pd
    import numpy as np

    # Component Shard 1: First split vertical partition of messy analytics tracking data
    shard_1 = {
        "tx_id": [99001, 99002],
        "node_code": [" srv_mumbai ", "srv_delhi "],
        "latency_str": ["45.2", "120.8"],
        "team_tag": ["DATA_PLATFORM", "CORE_INFRA"],
    }
    df_shard_1 = pd.DataFrame(shard_1)
    # Component Shard 2: Second split vertical partition (Contains anomalies & text noise)
    shard_2 = {
        "tx_id": [99003, 99004, 99005],
        "node_code": [" srv_mumbai", " srv_noida ", "srv_REJECT"],
        "latency_str": ["88.4", "12.1", "9999.9"],
        "team_tag": ["DATA_PLATFORM", "SECURITY_OPS", "CORE_INFRA"],
    }
    df_shard_2 = pd.DataFrame(shard_2)

    df_raw_master = pd.concat([df_shard_1, df_shard_2]).reset_index(drop=True)
    mask = ~df_raw_master["node_code"].str.contains("REJECT")
    df_raw_master = df_raw_master[mask]
    df_raw_master["node_code"] = df_raw_master["node_code"].str.strip().str.upper()
    df_raw_master["latency_str"] = df_raw_master["latency_str"].astype("float")
    df_raw_master["performance_tier"] = np.where(
        df_raw_master["latency_str"] > 100, "HIGH_LATENCY", "NORMAL_LATENCY"
    )
    print(
        "Perf Before for node_code: ",
        df_raw_master.memory_usage(deep=True)["node_code"],
        "bytes",
        "\n",
    )
    print(
        "Perf Before for team_tag: ",
        df_raw_master.memory_usage(deep=True)["team_tag"],
        "bytes",
        "\n",
    )

    df_raw_master["node_code"] = df_raw_master["node_code"].astype("category")
    df_raw_master["team_tag"] = df_raw_master["team_tag"].astype("category")
    print(
        "Perf After for node_code: ",
        df_raw_master.memory_usage(deep=True)["node_code"],
        "bytes",
        "\n",
    )
    print(
        "Perf After for team_tag: ",
        df_raw_master.memory_usage(deep=True)["team_tag"],
        "bytes",
        "\n",
    )

    print("Out raw DF:\n", df_raw_master, "\n")

    metric_a = (
        df_raw_master.groupby("team_tag", observed=True)
        .agg(avg_latency=("latency_str", "mean"), total_tx=("tx_id", "count"))
        .reset_index()
    )
    print("First Metric:\n", metric_a, "\n")

    target_b = df_raw_master.pivot_table(
        index="tx_id", columns="node_code", values="latency_str", fill_value=0.0, observed=True
    ).reset_index()
    target_b.columns.name = None
    print("Pivot Table:\n", target_b)
```

### My Output Verification:

```
    Perf Before for node_code:  234 bytes 

    Perf Before for team_tag:  244 bytes 

    Perf After for node_code:  287 bytes 

    Perf After for team_tag:  294 bytes 

    Out raw DF:
       tx_id   node_code  latency_str       team_tag performance_tier
    0  99001  SRV_MUMBAI         45.2  DATA_PLATFORM   NORMAL_LATENCY
    1  99002   SRV_DELHI        120.8     CORE_INFRA     HIGH_LATENCY
    2  99003  SRV_MUMBAI         88.4  DATA_PLATFORM   NORMAL_LATENCY
    3  99004   SRV_NOIDA         12.1   SECURITY_OPS   NORMAL_LATENCY 

    First Metric:
            team_tag  avg_latency  total_tx
    0     CORE_INFRA        120.8         1
    1  DATA_PLATFORM         66.8         2
    2   SECURITY_OPS         12.1         1 

    Pivot Table:
       tx_id  SRV_DELHI  SRV_MUMBAI  SRV_NOIDA
    0  99001        0.0        45.2        0.0
    1  99002      120.8         0.0        0.0
    2  99003        0.0        88.4        0.0
    3  99004        0.0         0.0       12.1
```
