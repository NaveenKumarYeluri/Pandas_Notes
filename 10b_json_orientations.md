# Pandas JSON Output Orientation Reference Cheatsheet

When exporting tabular DataFrames using `.to_json()`, the structure must change shapes because JSON relies on nested key-value pairs rather than a strict row-and-column coordinate grid. The `orient` parameter controls this structural layout mapping.

---

## 1. Shared Baseline DataFrame

Every layout pattern example below is generated using this exact 2x2 data matrix:

```python
    import pandas as pd

    data = {
        'emp_id': [101, 102],
        'region': ['MUM', 'DEL']
    }
    df = pd.DataFrame(data)
```

## 2. The JSON Shapes (Part 1)

### Pattern A: orient='columns' (The Engine Default)

* **Behavior:** Maps the top-level keys directly to your column headers. Inside each column key, it nests another dictionary where row index numbers map to individual cell values.
* **Best Used For:** Standard data storage backups where you want to reconstruct the exact data layout matrix shape upon reload.

**Output:**

```
    {
        "emp_id": {
            "0": 101,
            "1": 102
        },
        "region": {
            "0": "MUM",
            "1": "DEL"
        }
    }
```

### Pattern B: orient='records' (The Stream Standard)

* **Behavior:** Flattens the entire grid structure into a standard Python list of self-contained object dictionaries. Every row transforms into a distinct key-value entry.
* **Best Used For:** Real-time data pipeline streams, API responses, and importing straight into NoSQL engines like MongoDB or AWS DynamoDB.

**Output:**

```
    [
        {
            "emp_id": 101,
            "region": "MUM"
        },
        {
            "emp_id": 102,
            "region": "DEL"
        }
    ]
```

### Pattern C: orient='index' (Row-First Layout)

* **Behavior:** Inverts the default columns pattern. The top-level root keys map directly to row index labels, and each index nests a dictionary mapping columns to their respective row values.
* **Best Used For:** Key-value lookups where you want to query rows quickly by their primary structural row address keys.

**Output:**

```
    {
        "0": {
            "emp_id": 101,
            "region": "MUM"
        },
        "1": {
            "emp_id": 102,
            "region": "DEL"
        }
    }
```

---

### Pattern D: orient='split' (Decoupled Schemas)

* **Behavior:** Splits the DataFrame layout down into three completely independent, raw list components: columns, index, and data.
* **Best Used For:** System tracking dashboards or low-overhead network communications where you want to minimize structural text repetitions.

**Output:**

```
    {
        "columns": ["emp_id", "region"],
        "index": [0, 1],
        "data": [
            [101, "MUM"],
            [102, "DEL"]
        ]
    }
```

---

### Pattern E: orient='values' (Raw Multi-Dimensional Array)

* **Behavior:** Strips out all column names and structural row labels completely. It drops only the raw row data cell entries as an un-labeled matrix array of lists.
* **Best Used For:** Supplying clean raw data parameters directly to deep learning array engines or primitive visualization plotting tools.

**Output:**

```
    [
        [101, "MUM"],
        [102, "DEL"]
    ]
```
