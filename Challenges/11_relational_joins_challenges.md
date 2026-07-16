# Pandas Relational Data Joins Practice Challenges

This document contains six progressive data engineering challenges designed to test relational joins, handling unmatched keys across Left and Inner joins, aligning different key names, and constructing a multi-table analytics pipeline.

---

## Challenge 1: The Infrastructure Metric Lookup (Basic Inner Join)

**Scenario:** A monitoring system tracks server resource metrics across instance codes. To map these records to their physical department owners, you must perform an inner join against a master infrastructure registry.

### Raw Data Input

```python
    import pandas as pd

    metrics = {
        'instance_code': ['SRV-01', 'SRV-02', 'SRV-03'],
        'cpu_utilization': [45.2, 88.7, 12.1]
    }
    df_metrics = pd.DataFrame(metrics)

    registry = {
        'instance_code': ['SRV-01', 'SRV-02', 'SRV-04'],
        'department': ['Engineering', 'Data_Platform', 'Security']
    }
    df_registry = pd.DataFrame(registry)
```

### Your Tasks:

1. Use `pd.merge()` to perform an **Inner Join** between `df_metrics` and `df_registry` on the shared `instance_code` column.
2. Save the merged DataFrame into a variable named `df_inner_join` and print it. Inspect why `SRV-03` and `SRV-04` are excluded from the output.

### My Solution:

```python
import pandas as pd

metrics = {
    "instance_code": ["SRV-01", "SRV-02", "SRV-03"],
    "cpu_utilization": [45.2, 88.7, 12.1],
}
df_metrics = pd.DataFrame(metrics)
registry = {
    "instance_code": ["SRV-01", "SRV-02", "SRV-04"],
    "department": ["Engineering", "Data_Platform", "Security"],
}
df_registry = pd.DataFrame(registry)

df_inner_join = pd.merge(df_metrics, df_registry, on="instance_code", how="inner")
print(df_inner_join, "\n\n", "SRV-03 and SRV-04 have no matching records in opposite table.")
```

### My Output Verification:

```
      instance_code  cpu_utilization     department
    0        SRV-01             45.2    Engineering
    1        SRV-02             88.7  Data_Platform 

    SRV-03 and SRV-04 have no matching records in opposite table.
```

---

## Challenge 2: Transaction Enrichment Safetynet (Left Join Lookup)

**Scenario:** A cloud billing table records high-frequency transactional data streams. Before running accounting summaries, you must enrich the records with region names from a lookup table. You must ensure no transaction rows are accidentally dropped, even if a lookup code is missing.

### Raw Data Input

```python
    import pandas as pd

    transactions = {
        'tx_id': [5001, 5002, 5003],
        'region_id': ['MUM', 'DEL', 'NOID_LEGACY'],
        'amount_usd': [1200.00, 450.50, 85.00]
    }
    df_tx = pd.DataFrame(transactions)

    regions = {
        'region_id': ['MUM', 'DEL', 'CHN'],
        'region_name': ['Mumbai', 'Delhi', 'Chennai']
    }
    df_regions = pd.DataFrame(regions)
```

### Your Tasks:

1. Merge `df_tx` and `df_regions` using a **Left Join** (`how='left'`) to preserve all records from the transaction table.
2. Save the result to `df_enriched_tx` and print it. Explain what value Pandas places under the `region_name` column for the unmatched legacy region record.

### My Solution:

```python
    import pandas as pd

    transactions = {
        "tx_id": [5001, 5002, 5003],
        "region_id": ["MUM", "DEL", "NOID_LEGACY"],
        "amount_usd": [1200.00, 450.50, 85.00],
    }
    df_tx = pd.DataFrame(transactions)

    regions = {"region_id": ["MUM", "DEL", "CHN"], "region_name": ["Mumbai", "Delhi", "Chennai"]}
    df_regions = pd.DataFrame(regions)

    df_enriched_tx = pd.merge(left=df_tx, right=df_regions, on="region_id", how="left")
    print(
        df_enriched_tx,
        "\n\n",
        "When left table has no matching record on right table then we will have NaN",
    )
```

### My Output Verification:

```
       tx_id    region_id  amount_usd region_name
    0   5001          MUM      1200.0      Mumbai
    1   5002          DEL       450.5       Delhi
    2   5003  NOID_LEGACY        85.0         NaN 

    When left table has no matching record on right table then we will have NaN
```

---

## Challenge 3: Resolving Key Schema Asymmetry (`left_on` & `right_on`)

**Scenario:** Two staging datasets arrive from different microservices. While they share a logical relationship, one system names the relational column index `account_handle` while the other labels it `user_id`. You must bridge this schema difference.

### Raw Data Input

```python
    import pandas as pd

    user_profiles = {
        'account_handle': ['usr_99', 'usr_88', 'usr_77'],
        'email': ['naveen@company.com', 'amit@company.com', 'sara@company.com']
    }
    df_users = pd.DataFrame(user_profiles)

    storage_usage = {
        'user_id': ['usr_88', 'usr_99', 'usr_66'],
        'allocated_gb': [150, 420, 85]
    }
    df_storage = pd.DataFrame(storage_usage)
```

### Your Tasks:

1. Merge `df_users` (Left table) and `df_storage` (Right table) using an **Inner Join**.
2. Resolve the matching columns dynamically using the explicit configuration parameters `left_on='account_handle'` and `right_on='user_id'`.
3. Print the result. Notice that *both* key columns are retained in the merged frame.

### My Solution:

```python
    import pandas as pd

    user_profiles = {
        "account_handle": ["usr_99", "usr_88", "usr_77"],
        "email": ["naveen@company.com", "amit@company.com", "sara@company.com"],
    }
    df_users = pd.DataFrame(user_profiles)
    storage_usage = {"user_id": ["usr_88", "usr_99", "usr_66"], "allocated_gb": [150, 420, 85]}
    df_storage = pd.DataFrame(storage_usage)

    df_inner_join = pd.merge(
        left=df_users, right=df_storage, left_on="account_handle", right_on="user_id", how="inner"
    )

    print(df_inner_join)
```

### My Output Verification:

```
      account_handle               email user_id  allocated_gb
    0         usr_99  naveen@company.com  usr_99           420
    1         usr_88    amit@company.com  usr_88           150
```

---

## Challenge 4: Reconciling Disparate Systems (Full Outer Join Matrix)

**Scenario:** A DevOps team compiles an audit sheet matching active servers against billing records. Some active servers have not generated invoices yet, while some legacy invoices belong to decommissioned assets. You must run a full reconciliation map.

### Raw Data Input

```python
    import pandas as pd

    active_servers = {
        'host_id': ['SRV-A', 'SRV-B', 'SRV-C'],
        'status': ['ONLINE', 'ONLINE', 'MAINTENANCE']
    }
    df_hosts = pd.DataFrame(active_servers)

    billing_records = {
        'host_id': ['SRV-B', 'SRV-C', 'SRV-D'],
        'invoice_usd': [150.00, 320.00, 45.00]
    }
    df_billing = pd.DataFrame(billing_records)
```

### Your Tasks:

1. Execute a **Full Outer Join** (`how='outer'`) between `df_hosts` and `df_billing` on `host_id`.
2. Save the output as `df_reconciliation` and print the grid matrix to trace the missing alignment blocks.

### My Solution:

```python
    import pandas as pd

    active_servers = {
        "host_id": ["SRV-A", "SRV-B", "SRV-C"],
        "status": ["ONLINE", "ONLINE", "MAINTENANCE"],
    }
    df_hosts = pd.DataFrame(active_servers)
    billing_records = {
        "host_id": ["SRV-B", "SRV-C", "SRV-D"],
        "invoice_usd": [150.00, 320.00, 45.00],
    }
    df_billing = pd.DataFrame(billing_records)

    df_reconciliation = pd.merge(left=df_hosts, right=df_billing, on="host_id", how="outer")
    print(df_reconciliation)
```

### My Output Verification:

```
      host_id       status  invoice_usd
    0   SRV-A       ONLINE          NaN --> Missing Alignment Block (No Invoice)
    1   SRV-B       ONLINE        150.0
    2   SRV-C  MAINTENANCE        320.0
    3   SRV-D          NaN         45.0 --> Missing Alignment Block (No Active Host)
```

---

## Challenge 5: Pruning Redundant Keys (Post-Join Cleanup)

**Scenario:** After executing an asymmetrical join using `left_on` and `right_on`, your resulting DataFrame contains duplicate columns holding identical join keys. You must drop the redundant column to prepare the schema for database ingestion.

### Raw Data Input

```python
    import pandas as pd

    df_left = pd.DataFrame({'client_code': ['C-01', 'C-02'], 'balance': [5000, 7500]})
    df_right = pd.DataFrame({'customer_id': ['C-01', 'C-02'], 'tier': ['Gold', 'Platinum']})
```

### Your Tasks:

1. Merge `df_left` and `df_right` matching `client_code` against `customer_id` using a standard inner lookup.
2. Immediately drop the redundant `customer_id` column horizontally from the grid layer using `.drop()`.
3. Print the cleaned table.

### My Solution:

```python
    import pandas as pd

    df_left = pd.DataFrame({"client_code": ["C-01", "C-02"], "balance": [5000, 7500]})
    df_right = pd.DataFrame({"customer_id": ["C-01", "C-02"], "tier": ["Gold", "Platinum"]})

    df_inner_join = pd.merge(
        left=df_left, right=df_right, how="inner", left_on="client_code", right_on="customer_id"
    ).drop(columns="customer_id")
    print(df_inner_join)
```

### My Output Verification:

```
      client_code  balance      tier
    0        C-01     5000      Gold
    1        C-02     7500  Platinum
```

---

## Challenge 6: The Multi-Table Analytical Pipeline (The Final Boss)

**Scenario:** An analytics workflow staging bucket drops three uncleaned raw tracking components: transaction rows, node regional lookups, and system status registries. You must construct an end-to-end data refinery script to clean text parameters, mask internal tags, merge multiple relational data vectors, aggregate processing summaries, and serialize the data arrays.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    # Component 1: Messy transactional records
    raw_tx = {
        'tx_id': [9001, 9002, 9003, 9004],
        'target_node': [' srv_01 ', 'srv_02 ', ' srv_01', 'srv_REJECT'],
        'data_processed_mb': ['45000', '120000', np.nan, '999999']
    }
    df_raw_tx = pd.DataFrame(raw_tx)

    # Component 2: Infrastructure region directories
    raw_nodes = {
        'node_code': ['SRV_01', 'SRV_02', 'SRV_03'],
        'region_name': ['Mumbai', 'Delhi', 'Noida']
    }
    df_raw_nodes = pd.DataFrame(raw_nodes)

    # Component 3: Administrative internal team details
    raw_departments = {
        'region': ['Mumbai', 'Delhi'],
        'owner_team': ['Data_Platform', 'Core_Infra']
    }
    df_raw_dept = pd.DataFrame(raw_departments)
```

### Your Tasks:

1. **Sanitize Primary Transaction Records:**
   * Use a **tilde (`~`) inversion mask operator** along with a vectorized string filter to drop rows where `target_node` contains the text `'REJECT'`.
   * Discard any rows missing data elements (`NaN`) inside the `data_processed_mb` column.
   * Strip out all whitespaces from `target_node` and convert the entire column to **uppercase**.
   * Cast the `data_processed_mb` column explicitly to a standard integer layout vector (`int64`).
2. **Execute First Relational Merge Layer:**
   * Join your sanitized transaction table with the `df_raw_nodes` lookup table using a **Left Join**. 
   * Match the tables by aligning the transaction `target_node` column against the registry `node_code` column.
3. **Execute Second Relational Merge Layer:**
   * Enrich your merged dataset further by joining it with the `df_raw_dept` table using another **Left Join**.
   * Match the tables by aligning the `region_name` column from the previous merge against the `region` column inside the department directory.
4. **Post-Join Schema Cleanup:**
   * Drop the redundant key columns (`node_code` and `region`) horizontally from the DataFrame structure grid.
   * Reset the operational layout row indices sequentially starting from `0` using the clean `drop=True` optimization rule.
5. **Compile High-Level Summary (Target A):**
   * Group your refined master DataFrame by the `owner_team` column.
   * Apply the `.agg()` tool window to calculate the **sum** of the `data_processed_mb` column (rename it to `total_mb_processed`) and the absolute **count** of records (rename it to `total_transactions`). Flatten the index securely.
6. **Reshape Pivot Data Matrix (Target B):**
   * Using the exact same refined master DataFrame *prior* to your Target A aggregation step, build an independent pivoted dashboard grid matrix.
   * Distribute `tx_id` vertically down the rows.
   * Rotate `region_name` unique markers horizontally to serve as header columns.
   * Map the `data_processed_mb` values at the intersections, forcing any blank structural intersection elements to populate with `0` automatically.
   * Flatten the pivot index tracker completely and wipe out the axis name metadata parameter artifacts. Print both Target A and Target B processed matrices.

### My Solution:

```python
    import pandas as pd
    import numpy as np

    # Component 1: Messy transactional records
    raw_tx = {
        "tx_id": [9001, 9002, 9003, 9004],
        "target_node": [" srv_01 ", "srv_02 ", " srv_01", "srv_REJECT"],
        "data_processed_mb": ["45000", "120000", np.nan, "999999"],
    }
    df_raw_tx = pd.DataFrame(raw_tx)
    # Component 2: Infrastructure region directories
    raw_nodes = {
        "node_code": ["SRV_01", "SRV_02", "SRV_03"],
        "region_name": ["Mumbai", "Delhi", "Noida"],
    }
    df_raw_nodes = pd.DataFrame(raw_nodes)
    # Component 3: Administrative internal team details
    raw_departments = {
        "region": ["Mumbai", "Delhi"],
        "owner_team": ["Data_Platform", "Core_Infra"],
    }
    df_raw_dept = pd.DataFrame(raw_departments)

    mask = ~df_raw_tx["target_node"].str.contains("REJECT")
    df_raw_tx = df_raw_tx[mask]
    df_raw_tx = df_raw_tx.dropna(axis=0, subset="data_processed_mb")
    df_raw_tx["target_node"] = df_raw_tx["target_node"].str.strip().str.upper()
    df_raw_tx["data_processed_mb"] = df_raw_tx["data_processed_mb"].astype("int")

    df_tx_nodes = pd.merge(
        left=df_raw_tx, right=df_raw_nodes, left_on="target_node", right_on="node_code", how="left"
    )

    df_tx_nodes_dept = pd.merge(
        left=df_tx_nodes, right=df_raw_dept, left_on="region_name", right_on="region", how="left"
    )

    df_tx_nodes_dept = df_tx_nodes_dept.drop(columns=["node_code", "region"]).reset_index(
        drop=True
    )
    print(df_tx_nodes_dept)

    metric_a = (
        df_tx_nodes_dept.groupby("owner_team")
        .agg(
            total_mb_processed=("data_processed_mb", "sum"), total_transactions=("tx_id", "count")
        )
        .reset_index()
    )
    print("\n", "Metric A:\n", metric_a, "\n")

    metric_b = df_tx_nodes_dept.pivot_table(
        index="tx_id", columns="region_name", values="data_processed_mb", fill_value=0
    ).reset_index()
    metric_b.columns.name = None
    print(metric_b)
```

### My Output Verification:

```
       tx_id target_node  data_processed_mb region_name     owner_team
    0   9001      SRV_01              45000      Mumbai  Data_Platform
    1   9002      SRV_02             120000       Delhi     Core_Infra

    Metric A:
        owner_team  total_mb_processed  total_transactions
    0     Core_Infra              120000                   1
    1  Data_Platform               45000                   1 

       tx_id     Delhi   Mumbai
    0   9001       0.0  45000.0
    1   9002  120000.0      0.0
```

