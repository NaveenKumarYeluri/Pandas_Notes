# Pandas Advanced Aggregations and Pivoting Practice Challenges

This document contains six progressive data engineering challenges designed to test multi-metric rollups using the `.agg()` framework, data reshaping via structural pivots, and handling multi-index outcomes.

---

## Challenge 1: The Multi-Region Storage Audit (Multi-Metric Rollup)

**Scenario:** A cloud storage monitor outputs operational batch records across multiple regions. Before generating an executive summary, you must calculate total capacity footprint metrics alongside peak transaction costs simultaneously.

### Raw Data Input

```python
    import pandas as pd

    telemetry = {
        'region': ['Mumbai', 'Mumbai', 'Delhi', 'Delhi', 'Noida'],
        'storage_gb': [500, 300, 120, 250, 450],
        'compute_cost_usd': [12.50, 8.20, 4.50, 6.10, 11.00]
    }
    df_telemetry = pd.DataFrame(telemetry)
```

### Your Tasks:

1. Group `df_telemetry` by the `region` column.
2. Use the `.agg()` method to calculate two metrics simultaneously:
   * The **sum** of the `storage_gb` column (rename it to `total_storage_gb`).
   * The **max** of the `compute_cost_usd` column (rename it to `peak_compute_cost`).
3. Reset the DataFrame index so `region` becomes a standard column vector again, and print the resulting table.

### My Solution:

```python
    import pandas as pd

    telemetry = {
        "region": ["Mumbai", "Mumbai", "Delhi", "Delhi", "Noida"],
        "storage_gb": [500, 300, 120, 250, 450],
        "compute_cost_usd": [12.50, 8.20, 4.50, 6.10, 11.00],
    }
    df_telemetry = pd.DataFrame(telemetry)

    df_metrics = (
        df_telemetry.groupby("region")
        .agg(total_storage_gb=("storage_gb", "sum"), peak_compute_cost=("compute_cost_usd", "max"))
        .reset_index()
    )
    print("Metrics:\n", df_metrics, "\n")
    print("Our original DF:\n", df_telemetry)
```

### My Output Verification:

```
    Metrics:
       region  total_storage_gb  peak_compute_cost
    0   Delhi               370                6.1
    1  Mumbai               800               12.5
    2   Noida               450               11.0 

    Our original DF:
       region  storage_gb  compute_cost_usd
    0  Mumbai         500              12.5
    1  Mumbai         300               8.2
    2   Delhi         120               4.5
    3   Delhi         250               6.1
    4   Noida         450              11.0
```

---

## Challenge 2: Shifting Service Allocations (Basic Pivot Table)

**Scenario:** A cloud billing system logs compute execution instances across multiple internal engineering environments. To optimize resource utilization visibility, you must rotate the infrastructure environment markers horizontally.

### Raw Data Input

```python
    import pandas as pd

    billing_logs = {
        'fiscal_year': [2025, 2025, 2026, 2026, 2026],
        'environment': ['PROD', 'DEV', 'PROD', 'DEV', 'TEST'],
        'cluster_count': [12, 45, 18, 50, 8]
    }
    df_billing = pd.DataFrame(billing_logs)
```

### Your Tasks:

1. Generate a structural pivot matrix using `df_billing.pivot_table()`.
2. Configure the horizontal index rows to represent the `fiscal_year`.
3. Rotate the unique values of the `environment` column to serve as your horizontal column headers.
4. Set the intersecting cell values to capture the **sum** of the `cluster_count` metric.
5. Flatten the resulting index layout structure using `.reset_index()` and print the table.

### My Solution:

```python
    import pandas as pd

    billing_logs = {
        "fiscal_year": [2025, 2025, 2026, 2026, 2026],
        "environment": ["PROD", "DEV", "PROD", "DEV", "TEST"],
        "cluster_count": [12, 45, 18, 50, 8],
    }
    df_billing = pd.DataFrame(billing_logs)

    df_pivot_marix = df_billing.pivot_table(
        index="fiscal_year", columns="environment", values="cluster_count", aggfunc="sum"
    ).reset_index()
    print("Matrix:\n", df_pivot_marix, "\n\nOur original DF:\n", df_billing)
```

### My Output Verification:

```
    Matrix:
    environment  fiscal_year   DEV  PROD  TEST
    0                   2025  45.0  12.0   NaN
    1                   2026  50.0  18.0   8.0 

    Our original DF:
       fiscal_year environment  cluster_count
    0         2025        PROD             12
    1         2025         DEV             45
    2         2026        PROD             18
    3         2026         DEV             50
    4         2026        TEST              8
```

---

## Challenge 3: Multi-Service Throughput Rollup (Complex GroupBy Aggregations)

**Scenario:** An application microservice gateway tracks transaction metrics across distinct production nodes. You must run a comprehensive diagnostic calculation that aggregates row volumes, checks mean execution times, and locates processing peaks in a single pipeline pass.

### Raw Data Input

```python
    import pandas as pd

    gateway_stream = {
        'service_name': ['Auth', 'Auth', 'Payment', 'Payment', 'Auth'],
        'response_ms': [45, 120, 250, 410, 55],
        'data_transferred_kb': [15, 40, 120, 310, 18]
    }
    df_gateway = pd.DataFrame(gateway_stream)
```

### Your Tasks:

1. Group the dataset coordinates by `service_name`.
2. Apply the `.agg()` method to calculate three distinct features:
   * The absolute **count** of entries inside the `response_ms` column (rename it to `total_requests`).
   * The **mean** average value of the `response_ms` column (rename it to `avg_latency_ms`).
   * The **max** peak value inside the `data_transferred_kb` column (rename it to `max_payload_kb`).
3. Reset the resulting layout index sequentially and print the summary frame.

### My Solution:

```python
    import pandas as pd

    gateway_stream = {
        "service_name": ["Auth", "Auth", "Payment", "Payment", "Auth"],
        "response_ms": [45, 120, 250, 410, 55],
        "data_transferred_kb": [15, 40, 120, 310, 18],
    }
    df_gateway = pd.DataFrame(gateway_stream)

    df_metrics = (
        df_gateway.groupby("service_name")
        .agg(
            total_requests=("response_ms", "count"),
            avg_latency_ms=("response_ms", "mean"),
            max_payload_kb=("data_transferred_kb", "max"),
        )
        .reset_index()
    )
    print(df_metrics)
```

### My Output Verification:

```
                  total_requests  avg_latency_ms  max_payload_kb
    service_name                                                
    Auth                       3       73.333333              40
    Payment                    2      330.000000             310
```

---

## Challenge 4: Regional Cost Distribution Audits (Alternative Pivot Aggregations)

**Scenario:** A multi-cluster resource log records deployment configurations. By default, pivots run a simple mean calculation. You must override this default parameter configuration behavior to audit peak resource expenditures instead.

### Raw Data Input

```python
    import pandas as pd

    cluster_costs = {
        'operating_system': ['Linux', 'Linux', 'Windows', 'Windows', 'Linux'],
        'region_zone': ['Mumbai', 'Delhi', 'Mumbai', 'Delhi', 'Mumbai'],
        'hourly_rate': [0.12, 0.15, 0.45, 0.48, 0.14]
    }
    df_costs = pd.DataFrame(cluster_costs)
```

### Your Tasks:

1. Build a structural pivot table mapping `operating_system` down the vertical index rows.
2. Distribute `region_zone` markers horizontally across the header columns.
3. Configure the intersecting cells to evaluate the `hourly_rate` metrics using an explicit **max** aggregation function override argument switch.
4. Flatten the structural multi-index layer tracking boundaries using `.reset_index()` and print the layout matrix grid.

### My Solution:

```python
    import pandas as pd

    cluster_costs = {
        "operating_system": ["Linux", "Linux", "Windows", "Windows", "Linux"],
        "region_zone": ["Mumbai", "Delhi", "Mumbai", "Delhi", "Mumbai"],
        "hourly_rate": [0.12, 0.15, 0.45, 0.48, 0.14],
    }
    df_costs = pd.DataFrame(cluster_costs)

    df_pivot = df_costs.pivot_table(
        index="operating_system", columns="region_zone", values="hourly_rate", aggfunc="max"
    ).reset_index()

    print(df_pivot)
```

### My Output Verification:

```
    region_zone operating_system  Delhi  Mumbai
    0                      Linux   0.15    0.14
    1                    Windows   0.48    0.45
```

---

## Challenge 5: Managing Pivot Structural Nulls (The Missing Category Strategy)

**Scenario:** In complex data matrices, rotating columns horizontally often yields structural intersection points that contain zero historical entries, producing clean null parameter cells (`NaN`). You must ensure these blanks are normalized into active integer zeroes automatically during structural compilation.

### Raw Data Input

```python
    import pandas as pd

    regional_ledger = {
        'quarter': ['Q1', 'Q1', 'Q2', 'Q2'],
        'product_line': ['Cloud_Compute', 'SaaS_App', 'Cloud_Compute', 'Database_Storage'],
        'revenue_thousands': [150, 45, 180, 90]
    }
    df_ledger = pd.DataFrame(regional_ledger)
```

### Your Tasks:

1. Construct an analytical pivot grid tracking `quarter` vertically down the rows and rotating `product_line` entries horizontally to serve as column variables.
2. Aggregate the `revenue_thousands` metric columns using a **sum** function parameter.
3. Incorporate an explicit built-in keyword parameter mapping argument rule directly within the `.pivot_table()` command to force any missing intersection values to default to `0` instead of `NaN`.
4. Flatten the matrix layout frame index tracks and inspect the output cells.

### My Solution:

```python
    import pandas as pd

    regional_ledger = {
        "quarter": ["Q1", "Q1", "Q2", "Q2"],
        "product_line": ["Cloud_Compute", "SaaS_App", "Cloud_Compute", "Database_Storage"],
        "revenue_thousands": [150, 45, 180, 90],
    }
    df_ledger = pd.DataFrame(regional_ledger)

    df_pivot = df_ledger.pivot_table(
        index="quarter",
        columns="product_line",
        values="revenue_thousands",
        aggfunc="sum",
        fill_value=0,
    ).reset_index()

    print(df_pivot)
```

### My Output Verification:

```
    product_line quarter  Cloud_Compute  Database_Storage  SaaS_App
    0                 Q1            150                 0        45
    1                 Q2            180                90         0
```

---

## Challenge 6: The Automated Financial Cube Engineering Engine (The Final Boss)

**Scenario:** A staging environment landing container drops a messy transaction ledger. Before generating down-stream report targets or sending arrays to a NoSQL file system, you must scrub formatting text anomalies, recast spatial measurements, mask dead system fields, execute multi-metric rollups, and build an independent matrix pivot snapshot table.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    raw_transactions = {
        'tx_id': [9001, 9002, 9003, 9004, 9005],
        'cluster_zone': [' mum_prod ', 'del_test ', ' mum_prod', ' noid_prod ', 'del_REJECT'],
        'cost_str': ['1200.50', np.nan, '4500.00', '850.25', '9999.99'],
        'processing_hours': ['2.5', '1.2', '4.0', '0.8', '99.9'],
        'internal_token': ['t-1', 't-2', 't-3', 't-4', 't-5']
    }
    df_raw_cube = pd.DataFrame(raw_transactions)
```

### Your Tasks:

1. **Sanitize Operational Targets:** Use the **tilde (`~`) inversion mask operator** combined with a vectorized text match method to completely drop records where the `cluster_zone` column contains the text string `'REJECT'`.
2. **Purge Structural Empty Cells:** Discard any remaining database rows missing a valid tracking coordinate element within the `cost_str` configuration variable column subset.
3. **Normalize Layout String Vectors:** Strip out all leading/trailing whitespace components inside the entries of the `cluster_zone` column and transform the entire vector array to uppercase.
4. **Bulk Remap Memory Typings:** Use a type mapping configuration dictionary framework array to cast column vectors into their true mathematical target structures simultaneously:
   * Cast `cost_str` into standard float numbers (`float64`).
   * Cast `processing_hours` into precise float metrics (`float64`).
5. **Prune Tracking Artifact Column Features:** Drop the management identifier vector column named `internal_token` entirely from the grid dataframe layout layer.
6. **Compile Multi-Metric Summary (Target A):** Group the sanitized dataframe structure by `cluster_zone`. Use the `.agg()` tool window functions to simultaneously calculate the **sum** of `cost_str` (rename to `total_spend`) and the **mean** average of `processing_hours` (rename to `avg_hours`). Flatten the summary table index layout with `drop=True`.
7. **Reshape Pivot Data Matrix (Target B):** Using the exact same sanitized data frame snapshot asset layer *prior* to your Target A grouping step, construct a separate standalone pivoted data matrix structure table:
   * Map `tx_id` values down the vertical index rows.
   * Shift the sanitized `cluster_zone` unique value tags horizontally to serve as header columns.
   * Map the `cost_str` values at the intersections, forcing any empty missing structural coordinate cell parameters to fill with `0.0` automatically.
8. Reset the final pivoted table index structures securely so no duplicate tracker keys clutter memory layout views. Print both processed target summary tables.

### My Solution:

```python
    import pandas as pd
    import numpy as np

    raw_transactions = {
        "tx_id": [9001, 9002, 9003, 9004, 9005],
        "cluster_zone": [" mum_prod ", "del_test ", " mum_prod", " noid_prod ", "del_REJECT"],
        "cost_str": ["1200.50", np.nan, "4500.00", "850.25", "9999.99"],
        "processing_hours": ["2.5", "1.2", "4.0", "0.8", "99.9"],
        "internal_token": ["t-1", "t-2", "t-3", "t-4", "t-5"],
    }
    df_raw_cube = pd.DataFrame(raw_transactions)

    mask = ~df_raw_cube["cluster_zone"].str.contains("REJECT")
    df_raw_cube = df_raw_cube[mask]
    df_raw_cube = df_raw_cube.dropna(subset=["cost_str"])
    df_raw_cube["cluster_zone"] = df_raw_cube["cluster_zone"].str.strip().str.upper()

    type_map = {"cost_str": "float64", "processing_hours": "float64"}
    df_raw_cube = df_raw_cube.astype(type_map)

    df_raw_cube = df_raw_cube.drop(columns="internal_token").reset_index(drop=True)
    print("Our updated DF:\n", df_raw_cube, "\n")

    df_metrics = (
        df_raw_cube.groupby("cluster_zone")
        .agg(total_spend=("cost_str", "sum"), avg_hours=("processing_hours", "mean"))
        .reset_index()
    )
    print("Our GroupBy metrics:\n", df_metrics, "\n")

    df_pivot = df_raw_cube.pivot_table(
        index="tx_id",
        columns="cluster_zone",
        values="cost_str",
        fill_value=0.0,
    ).reset_index()

    df_pivot.columns.name = None
    print("Our Flattened Pivot Matrix:\n", df_pivot)
```

### My Output Verification:

```
    Unpivot data:
        year   env  cost
    0  2025  PROD  5000
    1  2025   DEV  1200
    2  2026  PROD  7500
    3  2026   DEV  1800
    4  2027   UAT   900 

    env  year     DEV    PROD    UAT
    0    2025  1200.0  5000.0    NaN
    1    2026  1800.0  7500.0    NaN
    2    2027     NaN     NaN  900.0
```

