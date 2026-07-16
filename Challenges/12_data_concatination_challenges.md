# Pandas Data Concatenation Practice Challenges

This document contains six progressive data engineering challenges designed to test vertical stacking, horizontal alignment, schema reconciliation for mismatched columns, and full end-to-end multi-source data consolidation.

---

## Challenge 1: Merging Incremental Batch Logs (Basic Vertical Stacking)

**Scenario:** An IoT pipeline drops sensor metrics in hourly streaming batches. To prepare the files for an aggregate daily report, you must stack two consecutive hourly data metrics segments vertically.

### Raw Data Input

```python
    import pandas as pd

    batch_01 = {
        'timestamp': ['09:00', '09:15', '09:30'],
        'device_id': ['I-10', 'I-11', 'I-10'],
        'reading': [45.2, 12.8, 44.9]
    }
    df_batch1 = pd.DataFrame(batch_01)

    batch_02 = {
        'timestamp': ['10:00', '10:15'],
        'device_id': ['I-10', 'I-12'],
        'reading': [46.1, 89.4]
    }
    df_batch2 = pd.DataFrame(batch_02)

```

### Your Tasks:

1. Use `pd.concat()` to stack `df_batch1` and `df_batch2` vertically along `axis=0`.
2. Ensure you pass the parameter flag that discards the separate raw row indexes, producing a single clean sequential tracking index.
3. Save the result into a variable named `df_daily_stream` and print it.

### My Solution:

```

```

### My Output Verification:

```

```

---

## Challenge 2: Consolidating Regional Infrastructure Nodes (Schema Alignment Stacking)

**Scenario:** Two multi-region cloud clusters dump active node inventory sheets. Both tables share the exact same structural columns, but the source systems exported them in completely different column placement sequences.

### Raw Data Input

```python
    import pandas as pd

    mumbai_nodes = {
        'node_id': ['MUM-1', 'MUM-2'],
        'capacity_gb': [500, 1000],
        'tier': ['PROD', 'DEV']
    }
    df_mumbai = pd.DataFrame(mumbai_nodes)

    delhi_nodes = {
        'tier': ['PROD', 'TEST'],
        'node_id': ['DEL-1', 'DEL-2'],
        'capacity_gb': [750, 250]
    }
    df_delhi = pd.DataFrame(delhi_nodes)

```

### Your Tasks:

1. Vertically concatenate `df_mumbai` and `df_delhi` into a single unified tracking registry called `df_all_nodes`.
2. Inspect your output grid verification. In a trailing print comment, document how the Pandas concatenation engine handled the mismatched column sequence during compilation.

### My Solution:

```

```

### My Output Verification:

```

```

---

## Challenge 3: Harmonizing Structural Variations (Mismatched Column Ingestion)

**Scenario:** An analytics workflow ingests user activity logs from two legacy software builds. Build v1 records text comments, whereas Build v2 drops text comments but logs automated numeric event codes. You must stack these variants cleanly without losing version metrics.

### Raw Data Input

```python
    import pandas as pd

    logs_v1 = {
        'session_id': [801, 802],
        'user_handle': ['naveen', 'amit'],
        'user_comment': ['Access granted', 'Timeout error']
    }
    df_v1 = pd.DataFrame(logs_v1)

    logs_v2 = {
        'session_id': [803, 804],
        'user_handle': ['sara', 'rajesh'],
        'event_code': [1002, 5004]
    }
    df_v2 = pd.DataFrame(logs_v2)

```

### Your Tasks:

1. Use `pd.concat()` to vertically join `df_v1` and `df_v2` into a unified layout master named `df_master_logs` with a reset sequential index tracker.
2. Print the table layout grid and analyze what structure elements Pandas auto-populates for the fields unique to each version build.

### My Solution:

```

```

### My Output Verification:

```

```

---

## Challenge 4: Horizontal Side-by-Side Verification (Axis 1 Concatenation)

**Scenario:** An automated quality control worker runs an validation script that calculates analytical predictions side-by-side with true source data metrics. You need to combine these two independent, equal-length matrices horizontally.

### Raw Data Input

```python
    import pandas as pd

    source_metrics = pd.DataFrame({
        'account_id': [4401, 4402, 4403],
        'actual_spend': [1200, 450, 3100]
    })

    forecast_metrics = pd.DataFrame({
        'predicted_spend': [1250, 400, 3000],
        'variance_pct': [4.1, -11.1, -3.2]
    })

```

### Your Tasks:

1. Concatenate `source_metrics` and `forecast_metrics` horizontally side-by-side by explicitly modifying the `axis` parameter configuration.
2. Save the result to `df_validation_cube` and print it.

### My Solution:

```

```

### My Output Verification:

```

```

---

## Challenge 5: Multi-Source Bulk List Consolidation (Iterative List Stacking)

**Scenario:** A data warehouse orchestrator reads telemetry snippets from multiple isolated edge containers. Instead of running continuous individual row appends (which degrades computer memory performance), you must pass all individual structures inside a unified Python collection block to execute a high-speed bulk consolidation pass.

### Raw Data Input

```python
    import pandas as pd

    c1 = pd.DataFrame({'node': ['N1'], 'load': [45.2]})
    c2 = pd.DataFrame({'node': ['N2'], 'load': [88.1]})
    c3 = pd.DataFrame({'node': ['N3'], 'load': [12.4]})
    c4 = pd.DataFrame({'node': ['N4'], 'load': [55.9]})

```

### Your Tasks:

1. Construct a standard Python list named `container_registry_list` that aggregates all 4 individual DataFrame variables (`c1`, `c2`, `c3`, `c4`).
2. Pass the entire list block array directly into a single `pd.concat()` code statement.
3. Suppress the index trackers to ensure clean sequential rows, and print the resulting multi-node matrix.

### My Solution:

```

```

### My Output Verification:

```

```

---

## Challenge 6: The Distributed Multi-Source Integration Pipeline (The Final Boss)

**Scenario:** An enterprise migration pipeline reads uncleaned tracking logs dropped from three raw staging nodes: two split vertical partitions of transactional telemetry tables, and an isolated infrastructure lookup file. You must construct an end-to-end refinery component script to clean string errors, purge system nulls, execute bulk type casts, vertically concatenate partitioned streams, relational merge secondary metrics layers, run multi-metric rollups, and build an independent matrix pivot.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    # Partition 1: First half of raw streaming transactions
    partition_a = {
        'tx_id': [77001, 77002],
        'cluster_node': [' srv_mumbai ', 'srv_delhi '],
        'volume_str': ['150', '420']
    }
    df_part_a = pd.DataFrame(partition_a)

    # Partition 2: Second half of raw streaming transactions (Contains text anomalies & null elements)
    partition_b = {
        'tx_id': [77003, 77004, 77005],
        'cluster_node': [' srv_mumbai', ' srv_noida ', 'srv_REJECT'],
        'volume_str': ['88', np.nan, '99999']
    }
    df_part_b = pd.DataFrame(partition_b)

    # Lookup Component 3: Administrative metadata team registry
    infra_registry = {
        'node_code': ['SRV_MUMBAI', 'SRV_DELHI', 'SRV_NOIDA'],
        'owner_team': ['Data_Platform', 'Core_Infra', 'Security_Ops']
    }
    df_infra = pd.DataFrame(infra_registry)

```

### Your Tasks:

1. **Consolidate Partitioned Transaction Blocks:**
   * Vertically concatenate the raw `df_part_a` and `df_part_b` DataFrames into a single working structure variable named `df_raw_transactions`. Ensure raw duplicate index handles are suppressed completely.
2. **Sanitize Consolidated Telemetry Records:**
   * Use the **tilde (`~`) inversion mask operator** along with a vectorized string search to completely drop rows where `cluster_node` contains the text string `'REJECT'`.
   * Purge any remaining rows that are missing structural tracking elements (`NaN`) inside the `volume_str` parameter column subset.
   * Strip out all leading/trailing whitespace parameters from the entries of the `cluster_node` column and force the entire vector into uppercase lettering.
   * Cast the `volume_str` column explicitly into a standard integer layout vector (`int64`).
3. **Execute Horizontal Relational Enrichment Layer:**
   * Enrich your clean transactions dataset by matching it against the `df_infra` metadata registry using a standard **Left Join**.
   * Align the tables relationally by matching the updated transaction `cluster_node` column values against the lookup `node_code` column headers.
4. **Post-Join Schema Cleanup:**
   * Remove the duplicate key column `node_code` horizontally from the master tracking grid.
   * Reset the index tracks sequentially starting from `0` using the clean index reset optimization rule to ensure layout indices are fully normalized.
5. **Compile High-Level Summary Analysis (Target A):**
   * Group your refined master DataFrame by the `owner_team` column.
   * Apply the `.agg()` tool window functions to simultaneously calculate the **sum** of the `volume_str` column (rename it to `total_volume_processed`) and the absolute **count** of records (rename it to `active_transactions`). Flatten the index securely without discarding your critical grouping category labels.
6. **Reshape Pivot Data Matrix (Target B):**
   * Using the exact same refined master DataFrame *prior* to your Target A aggregation step, construct a separate standalone pivoted dashboard matrix structure table.
   * Map `tx_id` values down the vertical index rows.
   * Shift the sanitized `cluster_node` values horizontally to serve as header columns.
   * Map the `volume_str` numbers at the intersection coordinates, forcing any empty structural intersection cells to fill with `0` automatically.
   * Flatten the pivot matrix index layout structure cleanly, wipe out the floating axis name metadata parameter artifacts, and print both Target A and Target B processed matrices.

### My Solution:

```

```

### My Output Verification:

```

```

