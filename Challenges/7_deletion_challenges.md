# Pandas Structural Deletion Practice Challenges

This document contains six progressive data engineering challenges designed to test structural feature drops, automatic null purging, row subset targeting, and operational flag masking.

---

## Challenge 1: Pruning Extraneous API Metadata (Column Dropping)

**Scenario:** An application endpoint returns warehouse metrics alongside diagnostic system uptime arrays. Before exporting the data matrix to a production report, you must delete these internal telemetry variables.

### Raw Data Input

```python
    import pandas as pd

    warehouse_dump = {
        'sku_id': ['SKU-10', 'SKU-20', 'SKU-30'],
        'inventory_count': [450, 120, 890],
        'debug_sys_latency': [0.012, 0.045, 0.008],
        'api_version_tag': ['v2.4.1', 'v2.4.1', 'v2.4.1']
    }
    df_wh = pd.DataFrame(warehouse_dump)
```

### Your Tasks:

1. Use the `.drop()` method to permanently remove both `debug_sys_latency` and `api_version_tag` columns horizontally from the layout.
2. Store the result in a clean variable named `df_wh_report` and print it.

### My Solution:

```python
    import pandas as pd

    warehouse_dump = {
        "sku_id": ["SKU-10", "SKU-20", "SKU-30"],
        "inventory_count": [450, 120, 890],
        "debug_sys_latency": [0.012, 0.045, 0.008],
        "api_version_tag": ["v2.4.1", "v2.4.1", "v2.4.1"],
    }
    df_wh = pd.DataFrame(warehouse_dump)

    df_wh_report = df_wh.drop(columns=["debug_sys_latency", "api_version_tag"])
    print(df_wh_report)
```

### My Output Verification:

```
       sku_id  inventory_count
    0  SKU-10              450
    1  SKU-20              120
    2  SKU-30              890
```

---

## Challenge 2: Deleting Specific Erroneous Records (Row Dropping by Index)

**Scenario:** A data ingestion pipeline flags rows at index position `1` and `3` as containing garbled test payloads. You must drop these exact index rows before proceeding to aggregation.

### Raw Data Input

```python
    import pandas as pd

    transaction_stream = {
        'tx_id': [5501, 5502, 5503, 5504],
        'amount': [1200, 99999, 4500, 99999],
        'status': ['Success', 'Malformed_Test', 'Success', 'Malformed_Test']
    }
    df_tx = pd.DataFrame(transaction_stream)
```

### Your Tasks:

1. Use the `.drop()` method to target and eliminate rows vertically by passing a list containing the specific target index locations (`1` and `3`).
2. Print the final cleaned DataFrame layout.

### My Solution:

```python
    import pandas as pd

    transaction_stream = {
        "tx_id": [5501, 5502, 5503, 5504],
        "amount": [1200, 99999, 4500, 99999],
        "status": ["Success", "Malformed_Test", "Success", "Malformed_Test"],
    }
    df_tx = pd.DataFrame(transaction_stream)

    df_cleaned = df_tx.drop(index=[1, 3])
    print(df_cleaned)
```

### My Output Verification:

```
       tx_id  amount   status
    0   5501    1200  Success
    2   5503    4500  Success
```

---

## Challenge 3: Stripping Incomplete Transaction Logs (Automated Null Purging)

**Scenario:** A nightly transactional pipeline registers row entries where network dropouts have generated missing tracking blocks (`NaN`). To preserve arithmetic summary averages, any row missing **any** field must be completely discarded.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    payload = {
        'session_id': [101, 102, 103, 104],
        'user_handle': ['naveen', np.nan, 'amit', 'sara'],
        'billing_usd': [150.00, 45.00, np.nan, 320.00]
    }
    df_payload = pd.DataFrame(payload)
```

### Your Tasks:

1. Execute the `.dropna()` method on `df_payload` using the strict default rule that purges a row if it contains *any* missing attributes.
2. Save the result to `df_purged_logs` and verify the output row count.

### My Solution:

```python
    import pandas as pd
    import numpy as np

    payload = {
        "session_id": [101, 102, 103, 104],
        "user_handle": ["naveen", np.nan, "amit", "sara"],
        "billing_usd": [150.00, 45.00, np.nan, 320.00],
    }
    df_payload = pd.DataFrame(payload)

    df_purged_logs = df_payload.dropna(axis=0, how="any")
    print(df_purged_logs)
```

### My Output Verification:

```
       session_id user_handle  billing_usd
    0         101      naveen        150.0
    3         104        sara        320.0
```

---

## Challenge 4: Enforcing Mandatory Database Primary Keys (Subset Null Purging)

**Scenario:** A financial ledger dataset arrives with missing values. While missing user meta-tags can be safely ignored, rows missing the critical primary `account_balance` value must be explicitly dropped to protect financial tracking.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    ledger = {
        'ledger_id': [9001, 9002, 9003, 9004],
        'meta_tag': ['Cloud_AWS', 'SaaS_Tool', np.nan, 'Cloud_AWS'],
        'account_balance': [75000.00, np.nan, 12000.00, np.nan]
    }
    df_ledger = pd.DataFrame(ledger)
```

### Your Tasks:

1. Use the `.dropna()` method combined with the `subset` argument to isolate and scan *only* the `account_balance` column.
2. Keep rows where the balance is present, even if other parameters (like `meta_tag`) contain a null `NaN`.

### My Solution:

```python
    import pandas as pd
    import numpy as np

    ledger = {
        "ledger_id": [9001, 9002, 9003, 9004],
        "meta_tag": ["Cloud_AWS", "SaaS_Tool", np.nan, "Cloud_AWS"],
        "account_balance": [75000.00, np.nan, 12000.00, np.nan],
    }
    df_ledger = pd.DataFrame(ledger)

    df_ledger = df_ledger.dropna(subset="account_balance", axis=0)
    print(df_ledger)
```

### My Output Verification:

```
       ledger_id   meta_tag  account_balance
    0       9001  Cloud_AWS          75000.0
    2       9003        NaN          12000.0
```

---

## Challenge 5: Dropping Empty Structural Ghost Rows (The `how='all'` Safetynet)

**Scenario:** A file conversion script appends trailing separator breaks to a CSV stream, causing Pandas to ingest a row composed entirely of unresolvable `NaN` elements. You must clear these completely blank ghost entries without harming rows with partial information.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    csv_stream = {
        'item_id': [401, np.nan, 403, np.nan],
        'item_name': ['Server Rack', 'Patch Panel', np.nan, np.nan],
        'unit_cost': [25000, np.nan, 1200, np.nan]
    }
    df_stream = pd.DataFrame(csv_stream)
```

### Your Tasks:

1. Use `.dropna()` with the explicit configuration argument `how='all'` to target rows where **every single cell** is empty.
2. Print the table and verify that partially complete rows (such as row index `1` or `2`) are completely preserved.

### My Solution:

```python
    import pandas as pd
    import numpy as np

    csv_stream = {
        "item_id": [401, np.nan, 403, np.nan],
        "item_name": ["Server Rack", "Patch Panel", np.nan, np.nan],
        "unit_cost": [25000, np.nan, 1200, np.nan],
    }
    df_stream = pd.DataFrame(csv_stream)
    df_stream = df_stream.dropna(axis=0, how="all")
    print(df_stream)
```

### My Output Verification:

```
       item_id    item_name  unit_cost
    0    401.0  Server Rack    25000.0
    1      NaN  Patch Panel        NaN
    2    403.0          NaN     1200.0
```

---

## Challenge 6: The Pipeline Extraction and Deletion Engine (The Final Boss)

**Scenario:** You are writing an ingestion component for a streaming metrics server. The raw telemetry batch contains system metadata, dead accounts, legacy identifier headers, and incomplete entries. You must chain an optimization pipeline to drop features, isolate active records using an inversion mask, and cleanse remaining records of null fields.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    raw_telemetry = {
        'node_id': [7701, 7702, 7703, 7704, 7705],
        'cluster_tag': ['MUM_PROD', 'DEL_TEST', 'MUM_PROD', 'NOID_PROD', 'MUM_REJECT'],
        'cpu_load_pct': [45.2, np.nan, 88.1, 12.5, 99.9],
        'memory_gb': [64, 32, np.nan, 16, 128],
        'internal_api_key': ['x992', 'x112', 'x883', 'x441', 'x000']
    }
    df_raw = pd.DataFrame(raw_telemetry)
```

### Your Tasks:

1. **Strip Internal Keys:** Drop the administrative `internal_api_key` column entirely from the primary DataFrame grid.
2. **Execute Tilde Inversion Filtering:** Build a text-based search mask using `.str.contains()` to detect rows where the `cluster_tag` contains the phrase `'REJECT'`. Use the **tilde (`~`) operator** to invert the mask and drop those rows instantly without compiling lists.
3. **Purge Remaining System Nulls:** Clean the remaining valid rows of unresolvable operational data points. If a row contains a missing value inside **either** `cpu_load_pct` **or** `memory_gb`, drop the row entirely.
4. Reset the final structural index row handles sequentially starting from `0`. Print the finalized telemetry dashboard.

### My Solution:

```python
    import pandas as pd
    import numpy as np

    raw_telemetry = {
        "node_id": [7701, 7702, 7703, 7704, 7705],
        "cluster_tag": [
            "MUM_PROD",
            "DEL_TEST",
            "MUM_PROD",
            "NOID_PROD",
            "MUM_REJECT",
        ],
        "cpu_load_pct": [45.2, np.nan, 88.1, 12.5, 99.9],
        "memory_gb": [64, 32, np.nan, 16, 128],
        "internal_api_key": ["x992", "x112", "x883", "x441", "x000"],
    }
    df_raw = pd.DataFrame(raw_telemetry)

    df_raw = df_raw.drop(columns="internal_api_key")
    mask = ~df_raw["cluster_tag"].str.contains("REJECT")
    df_raw = df_raw[mask]
    df_raw = df_raw.dropna(
        axis=0, subset=["cpu_load_pct", "memory_gb"]
    ).reset_index(drop=True)
    print(df_raw)
```

### My Output Verification:

```
       node_id cluster_tag  cpu_load_pct  memory_gb
    0     7701    MUM_PROD          45.2       64.0
    1     7704   NOID_PROD          12.5       16.0
```
