# Stacking Data Vertically and Horizontally (`pd.concat()`)

While `pd.merge()` is designed to combine tables horizontally using relational keys (like a SQL JOIN), `pd.concat()` is designed to stack datasets directly along a specified axis without needing a matching column key (like a SQL UNION).

---

## 1. Vertical Stacking (Axis 0 - Stacking Rows)

The default behavior of `pd.concat()` is vertical stacking (`axis=0`). This takes multiple DataFrames that share the exact same column headers and joins them top-to-bottom.

### The Index Reset Requirement

When you stack tables vertically, Pandas will preserve each row's original index numbers. This results in duplicate row indices (e.g., multiple row `0`s, `1`s, etc.). To prevent this index fragmentation, you should always pass `ignore_index=True`.

### Vertical Stacking Example

```python
    import pandas as pd

    # Part 1: First batch of server telemetry
    df_mumbai = pd.DataFrame({
        'node_id': [7701, 7702],
        'status': ['ONLINE', 'ONLINE']
    })

    # Part 2: Second batch of server telemetry
    df_delhi = pd.DataFrame({
        'node_id': [8801, 8802],
        'status': ['MAINTENANCE', 'ONLINE']
    })

    # Stack them vertically into a single unified tracking registry
    df_all_nodes = pd.concat([df_mumbai, df_delhi], axis=0, ignore_index=True)
    print(df_all_nodes)
```

**Expected Output:**

```
       node_id       status
    0     7701       ONLINE
    1     7702       ONLINE
    2     8801  MAINTENANCE
    3     8802       ONLINE
```

---

## 2. Handling Column Schema Mismatches

If you attempt to vertically concatenate two DataFrames that do not have the exact same column names, Pandas will still combine them. However, it will expand the dataset horizontally to include all unique columns from both sides, filling the missing coordinates with null values (`NaN`).

### Mismatched Stacking Example

```python
    import pandas as pd

    df_a = pd.DataFrame({'id': [101], 'temp': [23.5]})
    df_b = pd.DataFrame({'id': [102], 'humidity': [45]})

    df_mismatched = pd.concat([df_a, df_b], axis=0, ignore_index=True)
    print(df_mismatched)
```

**Expected Output:**

```
        id  temp  humidity
    0  101  23.5       NaN
    1  102   NaN      45.0
```
