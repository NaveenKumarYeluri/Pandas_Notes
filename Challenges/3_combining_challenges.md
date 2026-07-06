# Pandas Combining Datasets Practice Challenges

This document contains six progressive data engineering challenges designed to test relational merging, horizontal schema alignment, vertical logging concatenation, and structural index normalization.

---

## Challenge 1: The Active User Session Audit (Inner Join)

**Scenario:** An application database logs user registrations in one table and active server login sessions in another. To prepare an operations report, you must isolate only the users who are currently logged into the system.

### Raw Data Input

```python
    import pandas as pd

    users = {
        'user_id': ['U101', 'U102', 'U103', 'U104'],
        'username': ['alpha', 'beta', 'gamma', 'delta']
    }
    sessions = {
        'user_id': ['U101', 'U102', 'U105'],
        'session_status': ['Active', 'Active', 'Idle']
    }
    df_users = pd.DataFrame(users)
    df_sessions = pd.DataFrame(sessions)
```

### Your Tasks:
1. Merge `df_users` and `df_sessions` using an inner join based on the shared `user_id` key. Assign the result to `df_active_audit`.
2. Inspect the final row count. Explain why user `U103` and user `U105` were excluded from the final output grid based on inner join rules.

### My Solution:

```python

```

### My Output Verification:

```

```

---

## Challenge 2: Complete User Profile Enrichment (Left Join)

**Scenario:** A marketing analytics job requires a master list of all registered users alongside their premium subscription details, if any exist. Unsubscribed users must remain in the tracking grid.

### Raw Data Input

```python
    import pandas as pd

    profiles = {
        'user_id': ['U101', 'U102', 'U103', 'U104'],
        'country': ['India', 'USA', 'India', 'UK']
    }
    subs = {
        'user_id': ['U101', 'U103'],
        'tier': ['Premium', 'Basic']
    }
    df_profiles = pd.DataFrame(profiles)
    df_subs = pd.DataFrame(subs)
```

### Your Tasks:
1. Perform a left join to combine `df_profiles` with `df_subs` using `user_id` as the join key, ensuring every user from `df_profiles` is preserved.
2. Observe the missing values generated in the output. What explicit placeholder does Pandas inject into the `tier` column for users who do not have a corresponding record in the subscription table?

### My Solution:

```python

```

### My Output Verification:

```

```

---

## Challenge 3: Mismatched Schema Keys (`left_on` / `right_on`)

**Scenario:** You are merging two tables extracted from different database microservices. The columns represent the exact same entity identifier, but the engineers named the keys differently: one uses `employee_id` and the other uses `emp_code`.

### Raw Data Input

```python
    import pandas as pd

    emp_registry = {
        'employee_id': [901, 902, 903],
        'full_name': ['Naveen', 'Amit', 'Sara']
    }
    payrolls = {
        'emp_code': [901, 902, 903],
        'monthly_salary': [75000, 82000, 68000]
    }
    df_reg = pd.DataFrame(emp_registry)
    df_pay = pd.DataFrame(payrolls)
```

### Your Tasks:
1. Research or look up the `left_on` and `right_on` parameters within the `pd.merge()` documentation.
2. Execute a single-line horizontal merge combining `df_reg` and `df_pay` by explicitly passing the mismatched column names as keys. 

### My Solution:

```python

```

### My Output Verification:

```

```

---

## Challenge 4: Periodic Log Stacking (Vertical Concatenation)

**Scenario:** A monitoring system dumps infrastructure metrics hourly into independent DataFrames. You need to combine these independent files vertically into a single cumulative historical master table.

### Raw Data Input

```python
    import pandas as pd

    hour_1 = {'timestamp': ['09:00', '09:15'], 'errors': [2, 0]}
    hour_2 = {'timestamp': ['10:00', '10:30'], 'errors': [5, 1]}
    
    df_h1 = pd.DataFrame(hour_1)
    df_h2 = pd.DataFrame(hour_2)
```

### Your Tasks:
1. Concatenate `df_h1` and `df_h2` vertically along the row axis. Assign the output to `df_combined_logs`.
2. Print the row index labels of `df_combined_logs`. Explain the structural issue with these labels and apply the mandatory index cleanup method to fix them.

### My Solution:

```python

```

### My Output Verification:

```

```

---

## Challenge 5: Cross-Region Inventory Reconcile (Outer Join)

**Scenario:** You are auditing physical component stocks across two distinct regional fulfillment warehouses. You must generate a unified tracking grid that preserves every SKU code found in either warehouse.

### Raw Data Input

```python
    import pandas as pd

    wh_mumbai = {
        'sku_id': ['SKU-A', 'SKU-B', 'SKU-C'],
        'mumbai_stock': [150, 45, 90]
    }
    wh_delhi = {
        'sku_id': ['SKU-B', 'SKU-C', 'SKU-D'],
        'delhi_stock': [300, 120, 15]
    }
    df_mum = pd.DataFrame(wh_mumbai)
    df_del = pd.DataFrame(wh_delhi)
```

### Your Tasks:
1. Execute a merge operation using an Outer Join mapping the `sku_id` key.
2. In the resulting summary tracking grid, fill any missing `NaN` cell values with an explicit default integer parameter of `0`.

### My Solution:

```python

```

### My Output Verification:

```

```

---

## Challenge 6: The E-Commerce Transaction Enrichment Pipeline (The Final Boss)

**Scenario:** You receive a streaming batch of newly executed online transaction logs. Before loading this batch into an analytical data warehouse cluster, you must enrich the rows with merchant names from a registry, fill missing categorical items, stack them with a historical batch, and normalize the final structure.

### Raw Data Input

```python
    import pandas as pd

    # Inbound Stream Batch
    stream_batch = {
        'tx_id': [7701, 7702, 7703],
        'merchant_code': ['M_AWS', 'M_SAAS', 'M_AWS'],
        'amount': [4500, 1200, 3100]
    }
    df_stream = pd.DataFrame(stream_batch)

    # Corporate Merchant Registry
    merchant_registry = {
        'merchant_code': ['M_AWS', 'M_SAAS', 'M_HARDWARE'],
        'merchant_name': ['Amazon Web Services', 'SaaS Tool Provider', 'Hardware Vendor']
    }
    df_registry = pd.DataFrame(merchant_registry)

    # Historical Archive Batch (Already Processed)
    historical_archive = {
        'tx_id': [7699, 7700],
        'merchant_name': ['Hardware Vendor', 'Amazon Web Services'],
        'amount': [85000, 15000]
    }
    df_historical = pd.DataFrame(historical_archive)
```

### Your Tasks:
1. Enrich the inbound stream: Perform a horizontal merge matching `df_stream` to `df_registry` using a **left join** based on the `merchant_code` key.
2. Partitioning the columns: Restrict the columns of this newly enriched stream DataFrame to include *only* `tx_id`, `merchant_name`, and `amount`. Drop the code column.
3. Pipeline Consolidation: Combine the historical archive DataFrame (`df_historical`) and your newly partitioned stream DataFrame vertically into a single master tracking database.
4. Final Structural Sanitization: Flatten the layout and ensure all scrambled index row addresses are cleanly reset sequentially starting from `0`. Print the final master output.

### My Solution:

```python

```

### My Output Verification:

```

```
