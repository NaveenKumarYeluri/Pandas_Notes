# Pandas Structural Deletion and Missing Data Handling

Data purification pipelines frequently require pruning administrative columns, dropping corrupted data rows, or purging records containing unresolvable missing indicators (`NaN`). Pandas handles these removals using the `.drop()` and `.dropna()` methods.

---

## 1. Deleting Structural Grid Features (`.drop()`)

The `.drop()` method deletes specified index addresses or entire named column headers from a DataFrame grid layout structure.

### Core Parameter Controls

* **`columns`**: Accepts a single string name or a Python list of multiple named string headers to strip out horizontally.
* **`axis`**: Controls the orientation of the drop operation (`axis=0` targets rows vertically; `axis=1` targets columns horizontally). Specifying `columns=` automatically defaults the direction to `axis=1`.

**Example:**

```python
    import pandas as pd

    data = {
        'account_id': [1001, 1002],
        'username': ['naveen_k', 'amit_s'],
        'temporary_token': ['tkn_8823x', 'tkn_9912z']
    }
    df = pd.DataFrame(data)

    # Excise the administrative token column to protect data privacy
    df_cleaned = df.drop(columns=['temporary_token'])
    print(df_cleaned)
```

**Output:**

```
       account_id  username
    0        1001  naveen_k
    1        1002    amit_s
```

---

## 2. Automated Null Purging (`.dropna()`)

When whole rows or data cells contain null value markers (`NaN`), the `.dropna()` method inspects the grid data cells and automatically discards structures that violate clean state parameters.

### Core Argument Modifiers

* **`how='any'` (Default)**: Drops a row if even a single cell inside that row contains a null value marker.
* **`how='all'`**: Drops a row *only* if every single column cell inside that exact row is completely filled with null parameters.
* **`subset`**: Restricts the operational lookup inspection to a specific, localized list of columns instead of scanning the whole table layout.

**Example:**

```python
    import pandas as pd
    import numpy as np

    data = {
        'tx_id': [501, 502, 503],
        'client': ['Naveen', 'Amit', np.nan],
        'amount': [25000, np.nan, np.nan]
    }
    df = pd.DataFrame(data)

    # Pipeline Strategy: Clean rows only if they are missing the critical 'amount' statistic
    df_sanitized = df.dropna(subset=['amount'])
    print(df_sanitized)
```

**Output:**

```
       tx_id  client   amount
    0    501  Naveen  25000.0
```
