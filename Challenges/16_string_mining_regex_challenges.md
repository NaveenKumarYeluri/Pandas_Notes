# Pandas String Mining and Regular Expressions Practice Challenges

This document contains six progressive data engineering challenges designed to test substring extraction, complex pattern text scrubbing, structural schema creation from raw text, and end-to-end multi-source unstructured log processing.

---

## Challenge 1: Extracting Encapsulated Numeric IDs (Basic Substring Mining)

**Scenario:** A messaging queue logs system tasks as plain text strings where the numerical entity identifier is enclosed within descriptive labels. You must extract this key directly into its own column.

### Raw Data Input

```python
    import pandas as pd

    raw_tasks = {
        'task_idx': [1, 2, 3],
        'log_entry': ['JOB_ID:77102_INIT', 'JOB_ID:88401_RUN', 'JOB_ID:99203_STOP']
    }
    df_tasks = pd.DataFrame(raw_tasks)
```

### Your Tasks:

1. Use `.str.extract()` with a regex capture group to isolate the digits immediately following `JOB_ID:`.
2. Save the extracted column as `extracted_job_id`.
3. Print the updated DataFrame layout.

### My Solution:

```python
    import pandas as pd

    raw_tasks = {
        "task_idx": [1, 2, 3],
        "log_entry": ["JOB_ID:77102_INIT", "JOB_ID:88401_RUN", "JOB_ID:99203_STOP"],
    }
    df_tasks = pd.DataFrame(raw_tasks)

    pattern = r"JOB_ID:(\d+)_"

    df_tasks["extracted_job_id"] = df_tasks["log_entry"].str.extract(pattern)
    print(df_tasks)
```

### My Output Verification:

```
       task_idx          log_entry extracted_job_id
    0         1  JOB_ID:77102_INIT            77102
    1         2   JOB_ID:88401_RUN            88401
    2         3  JOB_ID:99203_STOP            99203
```

---

## Challenge 2: Cleaning Dynamic Suffix Noise (Basic String Replacement)

**Scenario:** An analytics staging bucket contains regional infrastructure tags appended with erratic diagnostic build brackets (e.g., `[v1]`, `[v2]`). You must strip out this dynamic metadata trailing noise cleanly.

### Raw Data Input

```python
    import pandas as pd

    build_tags = {
        'node_id': [101, 102, 103],
        'region_tag': ['MUMBAI [v1]', 'DELHI [v2]', 'NOIDA [v1]']
    }
    df_regions = pd.DataFrame(build_tags)
```

### Your Tasks:

1. Use `.str.replace()` with a regular expression pattern to target the space, the opening bracket, the version metadata, and the closing bracket.
2. Ensure you pass the appropriate parameter configuration flag indicating that the pattern matches a regex expression.
3. Save the scrubbed output back to the `region_tag` column and print the DataFrame view.

### My Solution:

```python
    import pandas as pd

    build_tags = {
        "node_id": [101, 102, 103],
        "region_tag": ["MUMBAI [v1]", "DELHI [v2]", "NOIDA [v1]"],
    }
    df_regions = pd.DataFrame(build_tags)

    pattern = r"\s\[([a-z])(\d)\]"
    df_regions["region_tag"] = df_regions["region_tag"].str.replace(pattern, "", regex=True)
    print(df_regions)
```

### My Output Verification:

```
       node_id region_tag
    0      101     MUMBAI
    1      102      DELHI
    2      103      NOIDA
```

---

## Challenge 3: Parsing Complex Multi-Variable Logs (Multi-Group Extraction)

**Scenario:** A cluster security module logs access failures in a combined string array layout. To build an auditing security matrix, you need to extract the raw host address string and the HTTP response code concurrently into separate target lanes.

### Raw Data Input

```python
    import pandas as pd

    audit_dump = {
        'log_id': [4001, 4002],
        'payload': ['HOST:srv-mumbai_RESP:403_FAIL', 'HOST:srv-delhi_RESP:500_FAIL']
    }
    df_audit = pd.DataFrame(audit_dump)
```

### Your Tasks:

1. Construct a regular expression containing two separate capture groups `()` to mine both variables simultaneously from the `payload` string.
2. Use `.str.extract()` to map the values straight into two new structural columns: `host_node` and `status_code`.
3. Print the updated master database structure table.

### My Solution:

```python
    import pandas as pd

    audit_dump = {
        "log_id": [4001, 4002],
        "payload": ["HOST:srv-mumbai_RESP:403_FAIL", "HOST:srv-delhi_RESP:500_FAIL"],
    }
    df_audit = pd.DataFrame(audit_dump)

    pattern = r"HOST:([a-z-a-z]+)_RESP:(\d+)_"
    df_audit[["host_node", "status_code"]] = df_audit["payload"].str.extract(pattern)
    print(df_audit)
```

### My Output Verification:

```
       log_id                        payload   host_node status_code
    0    4001  HOST:srv-mumbai_RESP:403_FAIL  srv-mumbai         403
    1    4002   HOST:srv-delhi_RESP:500_FAIL   srv-delhi         500
```

---

## Challenge 4: Sanitizing Mixed Alpha-Numeric Tokens (Regex Token Cleaning)

**Scenario:** An asset database registers components using legacy inventory tokens contaminated with variable separator hashes (e.g., `#`, `-`, `_`) and numeric clutter. You must strip out all non-alphabetic noise to standardize the prefix.

### Raw Data Input

```python
    import pandas as pd

    inventory = {
        'item_id': [1, 2, 3],
        'raw_token': ['PRD#99-MUM', 'DEV_88-DEL', 'STG#12-NOD']
    }
    df_inv = pd.DataFrame(inventory)
```

### Your Tasks:

1. Use `.str.replace()` with a character class regex to identify and remove all numeric digits and special symbols (`#`, `-`), leaving only the alphabetical characters intact.
2. Save the normalized character vector layer to `clean_prefix` and print the resulting table frame view.

### My Solution:

```python
    import pandas as pd

    inventory = {"item_id": [1, 2, 3], "raw_token": ["PRD#99-MUM", "DEV_88-DEL", "STG#12-NOD"]}
    df_inv = pd.DataFrame(inventory)

    pattern = r"([#_]+)(\d+)"
    df_inv["clean_prefix"] = df_inv["raw_token"].str.replace(pattern, "", regex=True)
    print(df_inv)
```

### My Output Verification:

```
       item_id   raw_token clean_prefix
    0        1  PRD#99-MUM       PRD-MUM
    1        2  DEV_88-DEL       DEV-DEL
    2        3  STG#12-NOD       STG-NOD
```

---

## Challenge 5: Dynamic Metric Isolation (Extracting Float Patterns)

**Scenario:** A streaming metrics agent appends raw performance utilization data as trailing text strings. You must isolate the floating-point metrics layer out of the text block to execute mathematical aggregations downstream.

### Raw Data Input

```python
    import pandas as pd

    telemetry = {
        'metric_id': ['M1', 'M2', 'M3'],
        'raw_stream': ['cpu_load:45.2%', 'cpu_load:88.75%', 'cpu_load:12.0%']
    }
    df_metrics = pd.DataFrame(telemetry)
```

### Your Tasks:

1. Design a regular expression capture group that matches a sequence of digits containing a literal decimal point.
2. Extract the numbers from `raw_stream`, save them to a column named `utilization_pct`, and cast the values explicitly to a standard float layout vector (`float64`).
3. Print the updated data schema and DataFrame table view.

### My Solution:

```python
    import pandas as pd

    telemetry = {
        "metric_id": ["M1", "M2", "M3"],
        "raw_stream": ["cpu_load:45.2%", "cpu_load:88.75%", "cpu_load:12.0%"],
    }
    df_metrics = pd.DataFrame(telemetry)

    pattern = r"([(\d+).(\d+)]+)"
    df_metrics["utilization_pct"] = df_metrics["raw_stream"].str.extract(pattern)
    df_metrics["utilization_pct"] = df_metrics["utilization_pct"].astype("float")
    print(df_metrics)
```

### My Output Verification:

```
      metric_id       raw_stream  utilization_pct
    0        M1   cpu_load:45.2%            45.20
    1        M2  cpu_load:88.75%            88.75
    2        M3   cpu_load:12.0%            12.00
```

---

## Challenge 6: The Unstructured Log Mining Refinery Pipeline (The Final Boss)

**Scenario:** An enterprise security cluster dumps chaotic raw log partitions across separate distributed staging storage spaces. The log blocks arrive split, containing dirty whitespace parameters, corrupted timestamp markers, administrative token flags, and complex unstructured payload records containing nested variables. You must construct a high-speed data engineering ingestion pipeline to stack the partitions, sanitize the string indices, execute robust date parsing, mine multi-variable parameters using regular expressions, recast numeric layers, apply memory category tuning optimizations, and calculate an aggregate summary analysis grid.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    # Partition 1: Unstructured streaming logs sequence A
    part_a = {
        'log_idx': [5001, 5002],
        'cluster_node': [' srv_mumbai ', 'srv_delhi '],
        'timestamp_str': ['2026-07-20 09:00:00', '2026-07-20 09:05:15'],
        'unstructured_payload': ['[WARN] code:8801 cost:12.50usd', '[ERROR] code:4402 cost:120.80usd']
    }
    df_part_a = pd.DataFrame(part_a)

    # Partition 2: Unstructured streaming logs sequence B (Contains text anomalies & null tracking targets)
    part_b = {
        'log_idx': [5003, 5004, 5005],
        'cluster_node': [' srv_mumbai', ' srv_delhi ', 'srv_REJECT'],
        'timestamp_str': ['2026-07-20 09:12:30', 'CORRUPT_TIME', '2026-07-20 09:45:00'],
        'unstructured_payload': ['[WARN] code:8801 cost:45.00usd', '[WARN] code:9901 cost:8.45usd', '[ERROR] code:9999 cost:999.99usd']
    }
    df_part_b = pd.DataFrame(part_b)
```

### Your Tasks:

1. **Consolidate Distributed Staging Partitions:**
   * Vertically concatenate `df_part_a` and `df_part_b` into a single working structure variable named `df_raw_logs`. Suppress duplicate row indices.
2. **Apply Vector Cleanse and Inversion Masking:**
   * Drop rows where `cluster_node` contains the text string `'REJECT'` using the **tilde (`~`) inversion mask operator**.
   * Strip out all formatting whitespaces from entries inside the `cluster_node` column and force the text vector into **uppercase**.
3. **Robust Chronological Parsing & Ingestion Auditing:**
   * Transform `timestamp_str` into true `datetime64[ns]` timestamp objects using `pd.to_datetime()`. Ensure you pass the safety configuration argument that forces unparseable string noise to dissolve into `NaT` fields.
   * Purge any remaining rows that contain empty missing structural timestamp elements (`NaT`) from the dataset immediately *after* the type casting pass.
4. **Mine Unstructured Multi-Variable Parameters (Regex Extraction):**
   * Review the `unstructured_payload` layout schema. It contains three distinct analytical vectors: a bracketed status level, a system code, and a numeric cost float code.
   * Write a comprehensive regular expression using capture groups `()` to mine all three components simultaneously. The regex pattern should capture:
     * The level inside the brackets (e.g., `WARN`, `ERROR`).
     * The digit string directly following `code:`.
     * The decimal float number directly following `cost:` and preceding `usd`.
   * Apply `.str.extract()` on the `unstructured_payload` column to break these out into three new columns: `log_level`, `system_code`, and `billing_cost`.
5. **Horizontal Post-Extraction Cleanup & Recasting:**
   * Remove the original messy `unstructured_payload` column horizontally from the master tracking grid.
   * Cast the newly mined `billing_cost` column explicitly to a standard float format vector (`float64`).
   * Cast the `log_level` and `cluster_node` columns to the **`category`** datatype to ensure optimal production memory tuning.
   * Reset the master table indices sequentially starting from `0`.
6. **Compile Aggregated Metric Analytics (Target A):**
   * Group your refined master DataFrame by the `log_level` column, ensuring you configure your aggregation parameters to account for the optimized category tracking format layer (`observed=True`).
   * Apply the `.agg()` tool window functions to simultaneously calculate the **sum** of `billing_cost` (rename to `total_spend_usd`) and the absolute **count** of entries (rename to `total_logs`). Reset the index tracks cleanly so your grouping field labels are fully preserved as a normal column vector.
7. **Reshape Dashboard Pivot Matrix (Target B):**
   * Using the exact same refined master DataFrame *prior* to your Target A aggregation step, construct a separate standalone pivoted dashboard matrix structure table.
   * Distribute `system_code` values down the vertical index rows.
   * Shift unique `cluster_node` structural strings horizontally to serve as header columns.
   * Map the numeric `billing_cost` values at the intersections, forcing any empty structural cells to populate with `0.0` automatically (and pass `observed=True`).
   * Flatten the pivot matrix index layout structure cleanly, wipe out the floating axis name metadata parameter artifacts, and print both Target A and Target B processed matrices.

### My Solution:

```python
    import pandas as pd

    # Partition 1: Unstructured streaming logs sequence A
    part_a = {
        "log_idx": [5001, 5002],
        "cluster_node": [" srv_mumbai ", "srv_delhi "],
        "timestamp_str": ["2026-07-20 09:00:00", "2026-07-20 09:05:15"],
        "unstructured_payload": [
            "[WARN] code:8801 cost:12.50usd",
            "[ERROR] code:4402 cost:120.80usd",
        ],
    }
    df_part_a = pd.DataFrame(part_a)
    # Partition 2: Unstructured streaming logs sequence B (Contains text anomalies
    # & null tracking targets)
    part_b = {
        "log_idx": [5003, 5004, 5005],
        "cluster_node": [" srv_mumbai", " srv_delhi ", "srv_REJECT"],
        "timestamp_str": ["2026-07-20 09:12:30", "CORRUPT_TIME", "2026-07-20 09:45:00"],
        "unstructured_payload": [
            "[WARN] code:8801 cost:45.00usd",
            "[WARN] code:9901 cost:8.45usd",
            "[ERROR] code:9999 cost:999.99usd",
        ],
    }
    df_part_b = pd.DataFrame(part_b)

    df_raw_logs = pd.concat([df_part_a, df_part_b]).reset_index(drop=True)
    mask = ~df_raw_logs["cluster_node"].str.contains("REJECT")
    df_raw_logs = df_raw_logs[mask]
    df_raw_logs["cluster_node"] = df_raw_logs["cluster_node"].str.strip().str.upper()
    df_raw_logs["timestamp_str"] = pd.to_datetime(df_raw_logs["timestamp_str"], errors="coerce")
    df_raw_logs = df_raw_logs.dropna(subset=["timestamp_str"])

    # \[([A-Z]+)\] -> Captures log level inside brackets
    # code:(\d+)   -> Captures explicit integer system code
    # cost:([\d.]+) -> Captures raw float numeric values cleanly without truncation
    pattern = r"\[([A-Z]+)\] code:(\d+) cost:([\d.]+)"
    df_raw_logs[["log_level", "system_code", "billing_cost"]] = df_raw_logs[
        "unstructured_payload"
    ].str.extract(pattern)

    df_raw_logs = df_raw_logs.drop(columns=["unstructured_payload"])
    df_raw_logs["billing_cost"] = df_raw_logs["billing_cost"].astype("float")
    df_raw_logs[["log_level", "cluster_node"]] = df_raw_logs[["log_level", "cluster_node"]].astype(
        "category"
    )
    df_raw_logs = df_raw_logs.reset_index(drop=True)
    print(df_raw_logs, "\n")

    target_a = (
        df_raw_logs.groupby("log_level", observed=True)
        .agg(total_spend_usd=("billing_cost", "sum"), total_logs=("log_idx", "count"))
        .reset_index()
    )
    print(target_a, "\n")

    target_b = df_raw_logs.pivot_table(
        index="system_code",
        columns="cluster_node",
        values="billing_cost",
        aggfunc="sum",
        fill_value=0.0,
        observed=True,
    ).reset_index()
    target_b.columns.name = None
    print(target_b)
```

### My Output Verification:

```
       log_idx cluster_node       timestamp_str log_level system_code  billing_cost
    0     5001   SRV_MUMBAI 2026-07-20 09:00:00      WARN        8801          12.5
    1     5002    SRV_DELHI 2026-07-20 09:05:15     ERROR        4402         120.8
    2     5003   SRV_MUMBAI 2026-07-20 09:12:30      WARN        8801          45.0 

      log_level  total_spend_usd  total_logs
    0     ERROR            120.8           1
    1      WARN             57.5           2 

      system_code  SRV_DELHI  SRV_MUMBAI
    0        4402      120.8        0.00
    1        8801        0.0        57.5
```
