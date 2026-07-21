# Pandas Data Type Casting (`.astype()`)

Data frames dynamically assign data types (`dtypes`) to columns upon data load. However, external tracking streams routinely ingest structural data fields using incorrect data typings—such as rendering transactional balances as text strings or binary category flags as floating-point integers. To resolve this, you must explicitly enforce column types using `.astype()`.

---

## 1. Inspecting Data Typings (`.dtypes`)

Before converting columns, a data engineer must audit the structural footprint of the data grid using the `.dtypes` property helper.

### Core Type Handles

* **`int64`**: Standard 64-bit integer numbers (no decimals).
* **`float64`**: 64-bit floating-point numeric vectors (supports decimals).
* **`object` / `string`**: Standard text formats or mixed alphanumeric streams.
* **`bool`**: Binary structural switches (`True` or `False`).

---

## 2. Converting Types via `.astype()`

The `.astype()` method casts a selected column vector explicitly into an alternative type layout. It can accept a single type descriptor string or a full Python map dictionary to recast multiple features simultaneously.

### Single-Column Casting Example

```python
    import pandas as pd

    data = {
        'item_id': [5001, 5002],
        'raw_cost': ['1450.50', '2200.00']  # Ingested as plain string text objects
    }
    df = pd.DataFrame(data)
    print("--- Baseline Types --- \n", df.dtypes)

    # Convert the string column to a proper numeric float vector to allow math operations
    df['raw_cost'] = df['raw_cost'].astype('float64')
    print("\n--- Processed Types --- \n", df.dtypes)
```

**Output:**

```
    --- Baseline Types --- 
    item_id      int64
    raw_cost    object
    dtype: object

    --- Processed Types --- 
    item_id        int64
    raw_cost     float64
    dtype: object
```

---

## 3. Multi-Column Dictionary Mapping

To transform multiple mismatched data streams in a single operational step, pass a mapping dictionary to the `.astype()` method.

### Multi-Column Casting Example

```python
    import pandas as pd

    data = {
        'node_code': [101, 102, 103],
        'usage_count': ['45', '12', '89'],    # Should be int64
        'billing_rate': ['0.12', '0.05', '0.18'] # Should be float64
    }
    df = pd.DataFrame(data)

    # Define a clean type configuration map
    type_registry_map = {
        'usage_count': 'int64',
        'billing_rate': 'float64'
    }

    # Execute the bulk structural type cast
    df = df.astype(type_registry_map)
    print(df.dtypes)
```

**Output:**

```
    node_code         int64
    usage_count       int64
    billing_rate    float64
    dtype: object
```
