# Pandas Exploding and Unpacking Nested Arrays Practice Challenges

This document contains six progressive data engineering challenges designed to test list exploding, delimited string splitting, multi-column exploding, nested JSON unpacking, and end-to-end semi-structured log pipeline normalization.

---

## Challenge 1: Unpacking Embedded System Roles (Basic List Exploding)

**Scenario:** An identity management system exports user access profiles where permissions are stored as embedded Python lists. You must explode these permissions into individual rows to run role-based compliance audits.

### Raw Data Input

```python
    import pandas as pd

    user_profiles = {
        'user_id': [901, 902, 903],
        'access_roles': [['SEC_ADMIN', 'READ_ONLY'], ['READ_ONLY'], ['DATA_ENG', 'SYS_ADMIN']]
    }
    df_users = pd.DataFrame(user_profiles)
```

### Your Tasks:

1. Use `.explode()` on the `access_roles` column to expand each role into its own independent row.
2. Reset the DataFrame index starting from `0` and print the normalized DataFrame.

### My Solution:

```python
    import pandas as pd

    user_profiles = {
        "user_id": [901, 902, 903],
        "access_roles": [["SEC_ADMIN", "READ_ONLY"], ["READ_ONLY"], ["DATA_ENG", "SYS_ADMIN"]],
    }
    df_users = pd.DataFrame(user_profiles)

    df_users = df_users.explode("access_roles").reset_index(drop=True)
    print(df_users)
```

### My Output Verification:

```
       user_id access_roles
    0      901    SEC_ADMIN
    1      901    READ_ONLY
    2      902    READ_ONLY
    3      903     DATA_ENG
    4      903    SYS_ADMIN
```

---

## Challenge 2: Parsing Delimited Skill Tags (String Split + Explode)

**Scenario:** A cloud talent inventory registers candidate skills as single pipe-delimited text strings (e.g., `'AWS|PYTHON|SQL'`). You must split these strings into lists and explode them into clean relational rows.

### Raw Data Input

```python
    import pandas as pd

    candidate_data = {
        'candidate_id': ['C-101', 'C-102', 'C-103'],
        'skills_str': ['AWS|PYTHON|REDSHIFT', 'SQL|POSTGRES', 'DOCKER|KUBERNETES|AWS']
    }
    df_candidates = pd.DataFrame(candidate_data)
```

### Your Tasks:

1. Convert `skills_str` into a list of strings by splitting on the pipe (`|`) character using `.str.split()`.
2. Explode the resulting list column into separate rows using `.explode()`.
3. Save the result to `skill_tag`, drop the original `skills_str` column, reset the index, and print the DataFrame.

### My Solution:

```python
    import pandas as pd

    candidate_data = {
        "candidate_id": ["C-101", "C-102", "C-103"],
        "skills_str": ["AWS|PYTHON|REDSHIFT", "SQL|POSTGRES", "DOCKER|KUBERNETES|AWS"],
    }
    df_candidates = pd.DataFrame(candidate_data)

    df_candidates["skill_tag"] = df_candidates["skills_str"].str.split("|")
    df_candidates = df_candidates.drop(columns="skills_str")
    df_candidates = df_candidates.explode("skill_tag").reset_index(drop=1)
    print(df_candidates)
```

### My Output Verification:

```
      candidate_id   skill_tag
    0        C-101         AWS
    1        C-101      PYTHON
    2        C-101    REDSHIFT
    3        C-102         SQL
    4        C-102    POSTGRES
    5        C-103      DOCKER
    6        C-103  KUBERNETES
    7        C-103         AWS
```

---

## Challenge 3: Synchronized Dual-Column Exploding (Multi-Column Explode)

**Scenario:** A distributed build server logs compiled artifacts and their corresponding execution costs as parallel lists within the same record. You must explode both columns simultaneously so that each artifact stays perfectly aligned with its cost.

### Raw Data Input

```python
    import pandas as pd

    build_logs = {
        'build_id': ['B-8801', 'B-8802'],
        'artifacts': [['auth_service', 'billing_job'], ['web_ui']],
        'cost_usd': [[12.50, 45.00], [8.20]]
    }
    df_builds = pd.DataFrame(build_logs)
```

### Your Tasks:

1. Pass a list of column names `['artifacts', 'cost_usd']` into `.explode()` to explode both nested lists concurrently.
2. Reset the DataFrame index and print the synchronized tabular output.

### My Solution:

```python
    import pandas as pd

    build_logs = {
        "build_id": ["B-8801", "B-8802"],
        "artifacts": [["auth_service", "billing_job"], ["web_ui"]],
        "cost_usd": [[12.50, 45.00], [8.20]],
    }
    df_builds = pd.DataFrame(build_logs)

    df_builds = df_builds.explode(["artifacts", "cost_usd"])
    print(df_builds)
```

### My Output Verification:

```
      build_id     artifacts cost_usd
    0   B-8801  auth_service     12.5
    0   B-8801   billing_job     45.0
    1   B-8802        web_ui      8.2
```

---

## Challenge 4: Handling Empty and Null Array Payload (Explode with Missing Data)

**Scenario:** An API tracking endpoint records user interaction events. Some users have no tags recorded (`[]`), while others have missing data (`None`). You must analyze how `.explode()` handles empty array structures without dropping rows unintentionally.

### Raw Data Input

```python
    import pandas as pd

    event_payload = {
        'session_id': ['S1', 'S2', 'S3', 'S4'],
        'tags': [['click', 'view'], [], ['purchase'], None]
    }
    df_events = pd.DataFrame(event_payload)
```

### Your Tasks:

1. Apply `.explode()` on the `tags` column.
2. Fill any resulting missing values (`NaN` / `None`) in the exploded `tags` column with the default string `'NO_TAGS'`.
3. Reset the index and print the result.

### My Solution:

```python
    import pandas as pd

    event_payload = {
        "session_id": ["S1", "S2", "S3", "S4"],
        "tags": [["click", "view"], [], ["purchase"], None],
    }
    df_events = pd.DataFrame(event_payload)

    df_events = df_events.explode("tags")
    df_events["tags"] = df_events["tags"].fillna("NO_TAGS")
    print(df_events)
```

### My Output Verification:

```
      session_id      tags
    0         S1     click
    0         S1      view
    1         S2   NO_TAGS
    2         S3  purchase
    3         S4   NO_TAGS
```

---

## Challenge 5: Exploded Aggregations & Frequency Counts (Explode + GroupBy)

**Scenario:** An infrastructure monitoring tool logs cluster warning codes as delimited comma-separated text blocks. Management needs a breakdown of the total occurrence count for every individual warning code across all server clusters.

### Raw Data Input

```python
    import pandas as pd

    cluster_warnings = {
        'cluster_name': ['MUM_01', 'DEL_02', 'MUM_01', 'NOIDA_01'],
        'warning_codes': ['ERR_500, ERR_404', 'ERR_500', 'ERR_403, ERR_500,ERR_404', 'ERR_403']
    }
    df_warnings = pd.DataFrame(cluster_warnings)
```

### Your Tasks:

1. Split `warning_codes` on commas (`,`) and explode the resulting list into individual rows.
2. Strip out any accidental leading/trailing whitespaces from the exploded warning strings.
3. Group by the exploded warning codes and count the total number of times each unique warning code occurred across the entire infrastructure. Save this summary as `total_occurrences`, reset the index, and print it.

### My Solution:

```python
    import pandas as pd

    cluster_warnings = {
        "cluster_name": ["MUM_01", "DEL_02", "MUM_01", "NOIDA_01"],
        "warning_codes": ["ERR_500, ERR_404", "ERR_500", "ERR_403, ERR_500, ERR_404", "ERR_403"],
    }
    df_warnings = pd.DataFrame(cluster_warnings)

    df_warnings["warning_codes"] = df_warnings["warning_codes"].str.split(",")
    df_warnings = df_warnings.explode(column="warning_codes").reset_index(drop=True)
    df_warnings["warning_codes"] = df_warnings["warning_codes"].str.strip()
    print(df_warnings, "\n")

    total_occurrences = (
        df_warnings.groupby("warning_codes")
        .agg(codes_cnt=("warning_codes", "count"))
        .reset_index()
    )
    print(total_occurrences)
```

### My Output Verification:

```
      cluster_name warning_codes
    0       MUM_01       ERR_500
    1       MUM_01       ERR_404
    2       DEL_02       ERR_500
    3       MUM_01       ERR_403
    4       MUM_01       ERR_500
    5       MUM_01       ERR_404
    6     NOIDA_01       ERR_403 

      warning_codes  codes_cnt
    0       ERR_403          2
    1       ERR_404          2
    2       ERR_500          3
```

---

## Challenge 6: The Unstructured Nested JSON Refinery Pipeline (The Final Boss)

**Scenario:** A enterprise API gateway drops messy JSON-like payload shards into a raw data lake landing pool. The payloads arrive split across multiple horizontal shards, containing whitespace formatting noise, invalid timestamp entries, corrupt administrative node tags, and comma-delimited nested string arrays embedded in single cell records. You must construct a high-speed production data engineering pipeline to stack raw partitions, sanitize strings, parse timestamps, unpack nested array strings into relational rows, recast numeric metrics, apply category memory tuning optimizations, compute windowed partitions, and build a normalized dashboard matrix.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    # Partition A: First sequence of nested API payload logs
    shard_a = {
        'log_id': [77002, 77001],
        'node_id': [' srv_mumbai ', 'srv_delhi '],
        'timestamp_raw': ['2026-07-22 10:05:00', '2026-07-22 10:00:00'],
        'raw_payload': ['[WARN] services:auth,billing cost:15.50', '[ERROR] services:database cost:120.00']
    }
    df_shard_a = pd.DataFrame(shard_a)

    # Partition B: Second sequence of nested API payload logs (Contains bad timestamps & rejected nodes)
    shard_b = {
        'log_id': [77003, 77004, 77005],
        'node_id': [' srv_mumbai', ' srv_delhi ', 'srv_REJECT'],
        'timestamp_raw': ['2026-07-22 10:12:30', 'CORRUPT_TIME', '2026-07-22 10:45:00'],
        'raw_payload': ['[WARN] services:auth,gateway,storage cost:45.00', '[WARN] services:cache cost:8.50', '[ERROR] services:bad cost:999.99']
    }
    df_shard_b = pd.DataFrame(shard_b)
```

### Your Tasks:

1. **Consolidate Distributed Staging Partitions:**
   * Vertically concatenate `df_shard_a` and `df_shard_b` into a master DataFrame named `df_raw_master`. Ensure raw duplicate index tracking handles are suppressed.
2. **Apply Vector Cleanse and Inversion Masking:**
   * Drop rows where `node_id` contains `'REJECT'` (case-insensitive) using the **tilde (`~`) inversion mask operator**.
   * Strip formatting whitespaces from entries inside `node_id` and convert the characters to **uppercase**.
3. **Robust Chronological Parsing & Sorting:**
   * Transform `timestamp_raw` into true `datetime64[ns]` timestamp objects using `pd.to_datetime()`, forcing invalid text strings to dissolve safely into `NaT`.
   * Purge missing structural timestamp elements (`NaT`) immediately *after* the type casting pass.
   * Sort the DataFrame chronologically by `timestamp_raw` in **ascending order** and reset the index sequentially from `0`.
4. **Mine Unstructured Payloads (Regex Extraction):**
   * Review `raw_payload`. It contains two key elements: a comma-delimited service list string directly following `services:`, and a floating-point numeric cost following `cost:`.
   * Use `.str.extract()` with regular expression capture groups `()` to mine:
     * The service string (e.g., `'auth,billing'`).
     * The numeric cost value (e.g., `'15.50'`).
   * Save these extracted outputs to columns named `services_raw` and `billing_cost`. Drop the original `raw_payload` column.
5. **Normalize Nested Service Arrays (`.str.split()` + `.explode()`):**
   * Convert `services_raw` into a true Python list by splitting on commas (`,`).
   * Apply `.explode()` on `services_raw` to expand each service into its own independent tabular row.
   * Rename the exploded column to `service_name`, strip any residual whitespace from `service_name`, and force the string characters to **uppercase**. Drop `services_raw`.
6. **Recast & Optimize Memory Types:**
   * Cast `billing_cost` explicitly to `float64`.
   * Cast `node_id` and `service_name` to the memory-efficient **`category`** datatype.
   * Reset the row index cleanly from `0`.
7. **Compile Target Outputs:**
   * **Target A (Aggregated Summary):** Group the exploded DataFrame by `service_name` (pass `observed=True`). Calculate the total spend (`total_spend_usd` using sum) and total invocation count (`total_invocations` using count). Reset the index and print.
   * **Target B (Pivot Matrix):** Pivot the exploded DataFrame setting `index='log_id'`, `columns='service_name'`, `values='billing_cost'`, `fill_value=0.0`, and `aggfunc='sum'` (pass `observed=True`). Flatten the index layout, clear `columns.name`, and print the matrix.

### My Solution:

```python
    import pandas as pd

    # Partition A: First sequence of nested API payload logs
    shard_a = {
        "log_id": [77002, 77001],
        "node_id": [" srv_mumbai ", "srv_delhi "],
        "timestamp_raw": ["2026-07-22 10:05:00", "2026-07-22 10:00:00"],
        "raw_payload": [
            "[WARN] services:auth,billing cost:15.50",
            "[ERROR] services:database cost:120.00",
        ],
    }
    df_shard_a = pd.DataFrame(shard_a)
    # Partition B: Second sequence of nested API payload logs
    # (Contains bad timestamps & rejected nodes)
    shard_b = {
        "log_id": [77003, 77004, 77005],
        "node_id": [" srv_mumbai", " srv_delhi ", "srv_REJECT"],
        "timestamp_raw": ["2026-07-22 10:12:30", "CORRUPT_TIME", "2026-07-22 10:45:00"],
        "raw_payload": [
            "[WARN] services:auth,gateway,storage cost:45.00",
            "[WARN] services:cache cost:8.50",
            "[ERROR] services:bad cost:999.99",
        ],
    }
    df_shard_b = pd.DataFrame(shard_b)

    df_raw_master = pd.concat([df_shard_a, df_shard_b]).reset_index(drop=True)

    mask = ~df_raw_master["node_id"].str.contains("REJECT")
    df_raw_master = df_raw_master[mask]
    df_raw_master["node_id"] = df_raw_master["node_id"].str.strip().str.upper()

    df_raw_master["timestamp_raw"] = pd.to_datetime(
        df_raw_master["timestamp_raw"], errors="coerce"
    )
    df_raw_master = df_raw_master.dropna(subset="timestamp_raw")
    df_raw_master = df_raw_master.sort_values(by="timestamp_raw").reset_index(drop=True)

    pattern = r"services:([a-z,]+) cost:([\d.]+)"
    df_raw_master[["services_raw", "billing_cost"]] = df_raw_master["raw_payload"].str.extract(
        pattern
    )
    df_raw_master = df_raw_master.drop(columns="raw_payload")

    df_raw_master["services_raw"] = df_raw_master["services_raw"].str.split(",")
    df_raw_master = df_raw_master.explode("services_raw")
    df_raw_master = df_raw_master.rename(columns={"services_raw": "service_name"})
    df_raw_master["service_name"] = df_raw_master["service_name"].str.strip().str.upper()

    df_raw_master["billing_cost"] = df_raw_master["billing_cost"].astype("float")
    df_raw_master[["node_id", "service_name"]] = df_raw_master[["node_id", "service_name"]].astype(
        "category"
    )
    df_raw_master = df_raw_master.reset_index(drop=True)
    print(df_raw_master, "\n")

    target_a = (
        df_raw_master.groupby(by="service_name", observed=True)
        .agg(total_spend_usd=("billing_cost", "sum"), total_invocations=("service_name", "count"))
        .reset_index()
    )
    print(target_a, "\n")

    target_b = df_raw_master.pivot_table(
        index="log_id",
        columns="service_name",
        values="billing_cost",
        fill_value=0.0,
        aggfunc="sum",
        observed=True,
    ).reset_index()
    target_b.columns.name = None
    print(target_b)
```

### My Output Verification:

```
       log_id     node_id       timestamp_raw service_name  billing_cost
    0   77001   SRV_DELHI 2026-07-22 10:00:00     DATABASE         120.0
    1   77002  SRV_MUMBAI 2026-07-22 10:05:00         AUTH          15.5
    2   77002  SRV_MUMBAI 2026-07-22 10:05:00      BILLING          15.5
    3   77003  SRV_MUMBAI 2026-07-22 10:12:30         AUTH          45.0
    4   77003  SRV_MUMBAI 2026-07-22 10:12:30      GATEWAY          45.0
    5   77003  SRV_MUMBAI 2026-07-22 10:12:30      STORAGE          45.0 

      service_name  total_spend_usd  total_invocations
    0         AUTH             60.5                  2
    1      BILLING             15.5                  1
    2     DATABASE            120.0                  1
    3      GATEWAY             45.0                  1
    4      STORAGE             45.0                  1 

       log_id  AUTH BILLING DATABASE GATEWAY STORAGE
    0   77001   0.0     0.0    120.0     0.0     0.0
    1   77002  15.5    15.5      0.0     0.0     0.0
    2   77003  45.0     0.0      0.0    45.0    45.0
```
