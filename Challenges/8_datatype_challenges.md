# Pandas Data Type Casting Practice Challenges

This document contains six progressive data engineering challenges designed to test structural type verification, single-column vector conversions, bulk multi-column dictionary type casting, and edge-case string handling.

---

## Challenge 1: The Raw Telemetry Data Type Audit (Type Verification)

**Scenario:** A newly configured IoT sensor stream dumps packet variables into a raw DataFrame grid. Before building descriptive statistics, you must audit the system's baseline memory mapping data types.

### Raw Data Input

```python
    import pandas as pd

    sensor_batch = {
        'sensor_id': [7001, 7002, 7003],
        'temperature_raw': ['23.5', '24.1', '22.8'],
        'active_flag': [1, 0, 1]
    }
    df_sensor = pd.DataFrame(sensor_batch)
```

### Your Tasks:

1. Print the baseline data types (`.dtypes`) of the `df_sensor` grid.
2. In a trailing print statement or comment, identify which column is improperly stored as a plain text `object` string vector despite containing numeric measurements.

### My Solution:

```python
    import pandas as pd

    sensor_batch = {
        "sensor_id": [7001, 7002, 7003],
        "temperature_raw": ["23.5", "24.1", "22.8"],
        "active_flag": [1, 0, 1],
    }
    df_sensor = pd.DataFrame(sensor_batch)

    print(df_sensor.dtypes, "\n")
    print("temperature_raw has wrong data type.")
```

### My Output Verification:

```
    sensor_id           int64
    temperature_raw    object
    active_flag         int64
    dtype: object 

    temperature_raw has wrong data type.
```

---

## Challenge 2: Synchronizing Metric Vectors (Single-Column Float Cast)

**Scenario:** Following your diagnostic type audit, you must cast the raw temperature column into its proper mathematical format so downstream analytical aggregations can compute mean averages without crashing.

### Raw Data Input

```python
    import pandas as pd

    df_sensor = pd.DataFrame({
        'sensor_id': [7001, 7002, 7003],
        'temperature_raw': ['23.5', '24.1', '22.8'],
        'active_flag': [1, 0, 1]
    })
```

### Your Tasks:

1. Use the `.astype()` method to cast the `temperature_raw` column explicitly into a 64-bit floating-point numeric vector (`float64`).
2. Overwrite the original column or save it to a new column handle named `temperature_clean`.
3. Print the updated `.dtypes` grid to verify the conversion state.

### My Solution:

```python
    import pandas as pd

    df_sensor = pd.DataFrame(
        {
            "sensor_id": [7001, 7002, 7003],
            "temperature_raw": ["23.5", "24.1", "22.8"],
            "active_flag": [1, 0, 1],
        }
    )
    df_sensor["temperature_clean"] = df_sensor["temperature_raw"].astype("float64")
    print(df_sensor.dtypes)
```

### My Output Verification:

```
    sensor_id              int64
    temperature_raw       object
    active_flag            int64
    temperature_clean    float64
    dtype: object
```

---

## Challenge 3: Enforcing Binary State Switches (Boolean Type Casting)

**Scenario:** An application database flags user connectivity states using standard numeric binary coordinates (`1` and `0`). To optimize structural clarity for data visualization layers, you must cast these numbers into true boolean logical switches.

### Raw Data Input

```python
    import pandas as pd

    connection_logs = {
        'session_id': [9901, 9902, 9903],
        'is_encrypted_int': [1, 0, 0]
    }
    df_conn = pd.DataFrame(connection_logs)
```

### Your Tasks:

1. Cast the `is_encrypted_int` column vector explicitly into a primitive `bool` data type via `.astype()`.
2. Save the output to a new structural layer column named `is_encrypted_bool`.
3. Print the final table layout to verify that `1` maps to `True` and `0` maps to `False`.

### My Solution:

```python
    import pandas as pd

    connection_logs = {
        "session_id": [9901, 9902, 9903],
        "is_encrypted_int": [1, 0, 0],
    }
    df_conn = pd.DataFrame(connection_logs)
    df_conn["is_encrypted_bool"] = df_conn["is_encrypted_int"].astype("bool")
    print(df_conn)
```

### My Output Verification:

```
       session_id  is_encrypted_int  is_encrypted_bool
    0        9901                 1               True
    1        9902                 0              False
    2        9903                 0              False
```

---

## Challenge 4: Streamlining Ingestion Frameworks (Bulk Multi-Column Mapping)

**Scenario:** A multi-region storage cluster drops transaction summaries loaded entirely as text object strings. You must construct a type mapping registry to cast multiple metrics simultaneously in a single step.

### Raw Data Input

```python
    import pandas as pd

    raw_summary = {
        'tx_code': ['TX-A', 'TX-B', 'TX-C'],
        'volume_str': ['150', '420', '89'],
        'cost_per_unit_str': ['12.50', '8.25', '14.00']
    }
    df_summary = pd.DataFrame(raw_summary)
```

### Your Tasks:

1. Define a type mapping registry dictionary called `target_type_map` that associates `volume_str` with `int64` and `cost_per_unit_str` with `float64`.
2. Apply this dictionary to the `df_summary` grid via `.astype()` in a single operational code statement.
3. Print the updated `.dtypes` layout grid.

### My Solution:

```python
    import pandas as pd

    raw_summary = {
        "tx_code": ["TX-A", "TX-B", "TX-C"],
        "volume_str": ["150", "420", "89"],
        "cost_per_unit_str": ["12.50", "8.25", "14.00"],
    }
    df_summary = pd.DataFrame(raw_summary)

    target_type_map = {"volume_str": "int64", "cost_per_unit_str": "float64"}
    df_summary = df_summary.astype(target_type_map)
    print(df_summary.dtypes)
```

### My Output Verification:

```
    tx_code               object
    volume_str             int64
    cost_per_unit_str    float64
    dtype: object
```

---

## Challenge 5: Handling Mismatched Special Formatting (The Currency Symbol Trap)

**Scenario:** A commercial billing spreadsheet stores financial pricing numbers with prepended currency indicators (e.g., `"$150.50"`). Running a direct `.astype('float64')` on this column will throw a compilation error. You must sanitize the string vector layer before forcing the type transformation.

### Raw Data Input

```python
    import pandas as pd

    billing_stream = {
        'invoice_id': [4401, 4402, 4403],
        'amount_usd_raw': ['$4500.25', '$120.00', '$3150.80']
    }
    df_billing = pd.DataFrame(billing_stream)
```

### Your Tasks:

1. Use your vectorized string toolkit (`.str.replace()`) to strip out the dollar sign (`$`) prefix from the `amount_usd_raw` column elements.
2. Chain or follow this operation with the `.astype('float64')` command to cast the sanitized text elements safely into float values.
3. Print the updated DataFrame matrix layout.

### My Solution:

```python
    import pandas as pd

    billing_stream = {
        "invoice_id": [4401, 4402, 4403],
        "amount_usd_raw": ["$4500.25", "$120.00", "$3150.80"],
    }
    df_billing = pd.DataFrame(billing_stream)
    df_billing["amount_usd_raw"] = (
        df_billing["amount_usd_raw"]
        .str.replace("$", "", regex=False)
        .astype("float64")
    )
    print(df_billing)
```

### My Output Verification:

```
       invoice_id  amount_usd_raw
    0        4401         4500.25
    1        4402          120.00
    2        4403         3150.80
```

---

## Challenge 6: The Industrial Batch Ingestion Data Scrubbing Engine (The Final Boss)

**Scenario:** An external telemetry worker delivers an unformatted text-based diagnostic grid block containing transactional data profiles, system noise, and administrative metadata. You must establish a complete type sanitization script pipeline to extract, slice, filter, clean, and map elements into their true analytical database types.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    raw_batch = {
        'job_id': [5501, 5502, 5503, 5504, 5505],
        'system_code': ['SYS_MUM_101', 'SYS_DEL_102', 'SYS_MUM_103', 'SYS_NOID_104', 'SYS_REJECT_000'],
        'raw_processed_gb': ['120', '45', np.nan, '88', '999'],
        'latency_ms_str': ['0.012', '0.045', '0.008', '0.015', '0.999']
    }
    df_pipeline = pd.DataFrame(raw_batch)
```

### Your Tasks:

1. **Filter Operational Targets:** Use the **tilde (`~`) inversion mask operator** along with a vectorized string search method to discard any rows where the `system_code` column contains the phrase `'REJECT'`.
2. **Purge Structural Nulls:** Drop any remaining row data rows where the `raw_processed_gb` feature is missing a record coordinate element (`NaN`) by setting a strict localized subset purge parameter.
3. **Execute Bulk Data Type Casting:** Construct a type mapping dictionary to cast the remaining valid data records into true numeric warehouse vectors simultaneously:
   * Transform `raw_processed_gb` into a true integer vector (`int64`).
   * Transform `latency_ms_str` into a precise numeric float vector (`float64`).
4. **Clean Structural Index Tracks:** Reset the resulting table row index handles sequentially starting from `0` ensuring that no extra junk column views are created in memory during normalisation. Print the finalized analytical pipeline grid output matrix.

### My Solution:

```python
    import pandas as pd
    import numpy as np

    raw_batch = {
        "job_id": [5501, 5502, 5503, 5504, 5505],
        "system_code": [
            "SYS_MUM_101",
            "SYS_DEL_102",
            "SYS_MUM_103",
            "SYS_NOID_104",
            "SYS_REJECT_000",
        ],
        "raw_processed_gb": ["120", "45", np.nan, "88", "999"],
        "latency_ms_str": ["0.012", "0.045", "0.008", "0.015", "0.999"],
    }
    df_pipeline = pd.DataFrame(raw_batch)
    mask = ~df_pipeline["system_code"].str.contains("REJECT")
    df_pipeline = df_pipeline[mask]
    df_pipeline = df_pipeline.dropna(subset="raw_processed_gb")
    target_type_map = {"raw_processed_gb": "int64", "latency_ms_str": "float64"}
    df_pipeline = df_pipeline.astype(target_type_map).reset_index(drop=True)
    print(df_pipeline)
```

### My Output Verification:

```
       job_id   system_code  raw_processed_gb  latency_ms_str
    0    5501   SYS_MUM_101               120           0.012
    1    5502   SYS_DEL_102                45           0.045
    2    5504  SYS_NOID_104                88           0.015
```

