# Pandas Combining Datasets (Joins & Concatenations)

Data pipelines frequently ingest fragmented datasets from distinct database entities. To build unified analytical targets, data engineers must merge tables horizontally based on common keys or concatenate them vertically to append structural streams.

---

## 1. Horizontal Merging (`pd.merge()`)

The `pd.merge()` function executes relational database join operations identical to the `JOIN` logic in SQL. It matches rows from a **Left DataFrame** and a **Right DataFrame** based on a shared identifier column known as the **Join Key**.


### Core Join Types (`how` Parameter)

* **`how='inner'` (Default):** Retains only the rows where the join key matches in **both** DataFrames. Mismatched keys are discarded.
* **`how='left'`:** Preserves all records from the left table, bringing in matching records from the right table. Mismatched right coordinates are filled with `NaN`.
* **`how='right'`:** Preserves all records from the right table, bringing in matching records from the left table. Mismatched left coordinates are filled with `NaN`.
* **`how='outer'`:** Preserves all records from both tables. Any unmatched boundaries on either side are auto-filled with `NaN`.

---

**Example:**

```python
    import pandas as pd

    # Left Table: Core employee registry
    employees = {
        'emp_id': [101, 102, 103],
        'name': ['Naveen', 'Rahul', 'Priya']
    }
    df_emp = pd.DataFrame(employees)

    # Right Table: Department assignments
    departments = {
        'emp_id': [101, 102, 104],
        'dept': ['Data Eng', 'DevOps', 'Cloud Arch']
    }
    df_dept = pd.DataFrame(departments)

    # Execute an inner merge on the shared key 'emp_id'
    inner_join = pd.merge(df_emp, df_dept, on='emp_id', how='inner')
    print(inner_join)
```

**Output:**

```
    emp_id    name      dept
    0     101  Naveen  Data Eng
    1     102   Rahul    DevOps
```

---

### Handling Mismatched Keys (Left Join Example)

If the intent is to track all registered employees regardless of whether they have a formal department assignment, a Left Join ensures no primary entity data is lost.

**Example:**

```python
    import pandas as pd

    df_emp = pd.DataFrame({'emp_id': [101, 102, 103], 'name': ['Naveen', 'Rahul', 'Priya']})
    df_dept = pd.DataFrame({'emp_id': [101, 102, 104], 'dept': ['Data Eng', 'DevOps', 'Cloud Arch']})

    # Execute a left merge
    left_join = pd.merge(df_emp, df_dept, on='emp_id', how='left')
    print(left_join)
```

**Output:**

```
       emp_id    name      dept
    0     101  Naveen  Data Eng
    1     102   Rahul    DevOps
    2     103   Priya       NaN
```

---

## 2. Vertical Concatenation (`pd.concat()`)

While merging grows a table horizontally by adding columns, concatenation grows a table vertically by stacking rows. This pattern is commonly used when binding periodic logs (e.g., combining January and February transaction logs) that share the exact same column schema.


### Syntax Architecture
Unlike `.merge()`, which is called directly on a DataFrame instance, `pd.concat()` is a top-level Pandas function that accepts a Python **list** of DataFrames to stack.

```python
    combined_df = pd.concat([df1, df2], axis=0)
```

**Example:**

```python
    import pandas as pd

    # Batch 1 Log Ingestion
    log_jan = {
        'tx_id': [501, 502],
        'amount': [1500, 2400]
    }
    df_jan = pd.DataFrame(log_jan)

    # Batch 2 Log Ingestion
    log_feb = {
        'tx_id': [503, 504],
        'amount': [3100, 1200]
    }
    df_feb = pd.DataFrame(log_feb)

    # Concatenate tables vertically along the row axis (axis=0)
    df_stacked = pd.concat([df_jan, df_feb], axis=0)
    print("--- Stacked DataFrame ---")
    print(df_stacked)

    # Reset index to fix scrambled row addresses
    df_cleaned = df_stacked.reset_index(drop=True)
    print("\n--- Cleaned Index DataFrame ---")
    print(df_cleaned)
```

**Output:**

```
    --- Stacked DataFrame ---
       tx_id  amount
    0    501    1500
    1    502    2400
    0    503    3100
    1    504    1200

    --- Cleaned Index DataFrame ---
       tx_id  amount
    0    501    1500
    1    502    2400
    2    503    3100
    3    504    1200
```

> **⚠️ Critical Design Rule:** When vertical DataFrames are stacked, their original individual row index positions (`0, 1...`) are completely preserved. This results in duplicate row labels, as shown in the "Stacked" output. Applying `.reset_index(drop=True)` directly after concatenation is a mandatory data engineering step to restore standard structural alignment.
