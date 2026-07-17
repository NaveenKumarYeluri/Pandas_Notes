# Pandas Datetime Processing Practice Challenges

This document contains six progressive data engineering challenges designed to test string-to-timestamp recasting, handling corrupted data elements, isolating chronological meta-tags, calculating duration latencies, and building complete end-to-end processing timelines.

---

## Challenge 1: The Raw Telemetry Chronology Cast (Basic Datetime Parsing)

**Scenario:** An application microservice dumps job execution logs where the transaction marker is recorded as a standard text string. Before sorting or performing calendar partitioning, you must transform these text coordinates into true nanosecond timestamp vectors.

### Raw Data Input

    import pandas as pd

    job_dump = {
        'job_id': [9001, 9002, 9003],
        'execution_start_str': ['2026-07-15 08:30:00', '2026-07-15 09:15:22', '2026-07-15 14:00:05']
    }
    df_jobs = pd.DataFrame(job_dump)

### Your Tasks:
1. Use `pd.to_datetime()` to parse the `execution_start_str` column into a proper 64-bit nanosecond timestamp vector (`datetime64[ns]`).
2. Overwrite the original column or save it into a new column handle named `execution_start_clean`.
3. Print the updated `.dtypes` layout matrix grid to verify the conversion state.

### My Solution:
<!-- Write your code or answers below this line -->


### My Output Verification:
<!-- Paste your terminal execution outputs below this line -->


---

## Challenge 2: The Staging Drop Tank Defect (Coercing Damaged Timestamps)

**Scenario:** A multi-region streaming worker dropped transmission logs containing corrupted string noise artifacts in the date field. You must force the type conversion pipeline safely without letting unmatched records crash the entire ingestion component run.

### Raw Data Input

    import pandas as pd

    corrupted_batch = {
        'tx_id': [5501, 5502, 5503],
        'log_timestamp_raw': ['2026-07-16 11:20:00', 'BAD_ROUTER_LATENCY_DATA', '2026-07-16 11:25:45']
    }
    df_logs = pd.DataFrame(corrupted_batch)

### Your Tasks:
1. Cast `log_timestamp_raw` into true datetime parameters using `pd.to_datetime()`.
2. Apply the configuration argument that forces unparseable alphanumeric strings to dissolve into safe null timestamp tracking blocks (`NaT`).
3. Save the result in place or to a column named `log_timestamp_clean` and print the resulting DataFrame grid.

### My Solution:
<!-- Write your code or answers below this line -->


### My Output Verification:
<!-- Paste your terminal execution outputs below this line -->


---

## Challenge 3: Extracting Chronological Meta-Tags (Vectorized `.dt` Accessors)

**Scenario:** A business intelligence platform requires transaction tracking data to be bucketed by specific time windows. You need to strip down clean datetime vectors into independent calendar units without compiling loops.

### Raw Data Input

    import pandas as pd

    df_scheduler = pd.DataFrame({
        'scheduled_time': pd.to_datetime(['2026-04-12 08:15:00', '2026-07-17 18:45:30'])
    })

### Your Tasks:
1. Isolate the numerical calendar hours from `scheduled_time` and save them into a new structural layer named `hour_window`.
2. Extract the textual day name from `scheduled_time` and save it to a column named `weekday_label`.
3. Print the final layout grid matrix table view.

### My Solution:
<!-- Write your code or answers below this line -->


### My Output Verification:
<!-- Paste your terminal execution outputs below this line -->


---

## Challenge 4: Auditing Pipeline Processing Delays (Time Delta Calculation)

**Scenario:** A cluster orchestration engineer needs to benchmark execution delay bottlenecks. You must measure the exact mathematical time duration between an initial queue state entry and its final storage resolution write.

### Raw Data Input

    import pandas as pd

    latency_log = {
        'task_id': ['TASK-A1', 'TASK-B2'],
        'queued_at': pd.to_datetime(['2026-07-17 08:00:00', '2026-07-17 10:15:30']),
        'resolved_at': pd.to_datetime(['2026-07-17 08:05:45', '2026-07-17 10:45:15'])
    }
    df_latency = pd.DataFrame(latency_log)

### Your Tasks:
1. Calculate the processing duration gap by subtracting `queued_at` from `resolved_at`. Save this mapping layer into a column named `processing_duration`.
2. Use the `.dt` accessor registry on your new duration layer to pull the exact raw elapsed timeframe value converted entirely into standard **seconds** (save to a column named `duration_seconds`).
3. Print the updated analytical summary grid frame.

### My Solution:
<!-- Write your code or answers below this line -->


### My Output Verification:
<!-- Paste your terminal execution outputs below this line -->


---

## Challenge 5: Time Series Ingestion Bucketing (DateTime Group Summaries)

**Scenario:** A telemetry node measures hourly data volume load sizes. To build a localized capacity overview dashboard report, you must group metrics across specific time markers and execute multi-metric summary rollups.

### Raw Data Input

    import pandas as pd

    server_traffic = {
        'timestamp_str': ['2026-07-15 08:05:00', '2026-07-15 08:42:00', '2026-07-15 09:12:00', '2026-07-15 09:55:00'],
        'data_transferred_mb': [450, 120, 890, 310]
    }
    df_traffic = pd.DataFrame(server_traffic)

### Your Tasks:
1. Recast the raw `timestamp_str` text values into true datetime tracking vectors.
2. Use the `.dt.hour` attribute mapping helper to create a tracking dimension column named `hour_bucket`.
3. Group your updated DataFrame by `hour_bucket`, then execute an `.agg()` multi-metric calculation block to compute the **sum** of `data_transferred_mb` (rename to `total_mb`) and the absolute **count** of entries (rename to `total_requests`). Reset the index tracks cleanly so your grouping key is fully preserved as a column.

### My Solution:
<!-- Write your code or answers below this line -->


### My Output Verification:
<!-- Paste your terminal execution outputs below this line -->


---

## Challenge 6: The Automated Chrono-Ingestion Cleanse Engine (The Final Boss)

**Scenario:** A staging database landing pool ingests an unformatted, multi-source chunk profile layout grid containing corrupted text streams, processing time details, and administrative identifiers. You must establish a complete type sanitization script component pipeline to parse string dates, isolate hours, drop internal keys, vertically concatenate split streams, relational merge metadata dimensions, compile high-level metrics, and structure an independent pivot summary matrix.

### Raw Data Input

    import pandas as pd
    import numpy as np

    # Partition 1: Streaming transactions (v1 text payload)
    p1 = {
        'tx_id': [44001, 44002],
        'node_code': [' srv_mumbai ', 'srv_delhi '],
        'start_str': ['2026-07-17 08:15:00', '2026-07-17 09:02:10'],
        'end_str': ['2026-07-17 08:20:30', '2026-07-17 09:45:00']
    }
    df_p1 = pd.DataFrame(p1)

    # Partition 2: Streaming transactions (v2 payload with text noise & null fields)
    p2 = {
        'tx_id': [44003, 44004, 44005],
        'node_code': [' srv_mumbai', ' srv_delhi ', 'srv_REJECT'],
        'start_str': ['2026-07-17 08:45:00', '2026-07-17 14:10:00', 'CORRUPT_TIME'],
        'end_str': ['2026-07-17 09:15:00', np.nan, 'CORRUPT_TIME']
    }
    df_p2 = pd.DataFrame(p2)

    # Secondary Lookup Table: Administrative deployment registry
    infra_registry = {
        'cluster_id': ['SRV_MUMBAI', 'SRV_DELHI'],
        'owner_team': ['Data_Platform', 'Core_Infra']
    }
    df_infra = pd.DataFrame(infra_registry)

### Your Tasks:
1. **Consolidate Split Staging Input Streams:**
   * Vertically concatenate `df_p1` and `df_p2` into a unified master DataFrame named `df_raw_master`. Ensure duplicate row index tracking values are suppressed.
2. **Apply Vector Cleanse and Inversion Masking:**
   * Discard any rows where `node_code` matches the pattern string `'REJECT'` using the **tilde (`~`) inversion mask operator**.
   * Strip away all formatting whitespaces from entries inside the `node_code` column and force the text vector entirely to **uppercase**.
3. **Execute Robust Chronological Parsing:**
   * Transform both `start_str` and `end_str` columns into true `datetime64[ns]` timestamp objects using `pd.to_datetime()`. Ensure you pass the safety configuration argument that forces unparseable string noise to dissolve into `NaT` fields without crashing the runtime log process.
   * Purge any remaining rows that contain empty missing structural timestamp tracking elements (`NaT`) inside **either** `start_str` **or** `end_str` column list subsets.
4. **Isolate Latency Profiles & Chrono Meta-Tags:**
   * Subtract the clean start timestamps from the end timestamps to build a duration tracking metric layer column named `elapsed_time`.
   * Extract the exact raw total elapsed value of `elapsed_time` converted entirely into decimal **seconds** (save to a column named `latency_seconds`).
   * Extract the calendar hour from the start timestamp vector to build a time window partitioning slot column named `start_hour_bucket`.
5. **Horizontal Relational Enrichment Layer:**
   * Merge your clean transactions master DataFrame against the `df_infra` table registry via a standard **Left Join**. Align the tables by linking the transaction `node_code` values directly to the `cluster_id` header headers.
   * Drop the duplicate relational coordinate keys `cluster_id` horizontally from the master grid. Reset the resulting table row index labels sequentially starting from `0`.
6. **Compile Time-Series Group Summary (Target A):**
   * Group your refined master DataFrame by the `owner_team` column.
   * Apply the `.agg()` tool window functions to simultaneously calculate the **mean** average value of `latency_seconds` (rename to `avg_latency_sec`) and the absolute **count** of entries (rename to `total_processed_tx`). Flatten the index securely ensuring that your critical team identifier grouping label is fully preserved as a normal column.
7. **Reshape Dashboard Pivot Matrix (Target B):**
   * Using the exact same refined master DataFrame *prior* to your Target A aggregation step, construct a separate standalone pivoted dashboard matrix structure table.
   * Distribute `tx_id` values down the vertical index rows.
   * Rotate `node_code` tracking variables horizontally to serve as header columns.
   * Map the numeric `latency_seconds` values at the intersections, forcing any blank structural intersection elements to populate with `0.0` automatically.
   * Flatten the pivot matrix index layout structure cleanly, wipe out the floating axis name metadata parameter artifacts, and print both Target A and Target B processed matrices.

### My Solution:
<!-- Write your code or answers below this line -->


### My Output Verification:
<!-- Paste your terminal execution outputs below this line -->