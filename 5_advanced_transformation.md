# Pandas Advanced Data Transformation (`.apply()` and Custom Mapping)

Analytical workflows frequently require row-wise evaluation or complex string transformations that standard vectorized math operators cannot execute. When standard methods like arithmetic operators or `.str` accessors are insufficient, custom logic must be injected across the data grid.

---

## 1. Element-Wise Transformation (`.apply()` on a Series)

The `.apply()` method executes a specified Python function on every individual cell coordinate down a single column Series. This is highly effective for custom string parsing, structural categorization, or applying conditional classification thresholds.

### Lambda Function Architecture

Instead of writing a full, multi-line Python function using `def`, data pipelines often utilize inline, single-line anonymous functions called **Lambda Functions**.

```python
    df['new_column'] = df['source_column'].apply(lambda x: custom_logic_on_x)
```

**Example:**

```python
    import pandas as pd

    data = {
        'emp_id': [101, 102, 103],
        'email': ['NAVEEN@company.com', 'AMIT@company.com', 'SARA@company.com']
    }
    df = pd.DataFrame(data)

    # Standardize email strings to lowercase via an inline lambda expression
    df['email_clean'] = df['email'].apply(lambda val: val.lower())
    print(df)
```

**Output:**

```
       emp_id               email         email_clean
    0     101  NAVEEN@company.com  naveen@company.com
    1     102    AMIT@company.com    amit@company.com
    2     103    SARA@company.com    sara@company.com
```

---

## 2. Row-Wise Multi-Column Transformation (`.apply(axis=1)`)

When a calculation relies on evaluating data values across **multiple columns simultaneously within the same row**, `.apply()` can be executed over the entire DataFrame grid by declaring `axis=1`.

* **`axis=0` (Default):** Passes data column by column vertically down the layout.
* **`axis=1`:** Passes data row by row horizontally across the grid. The lambda function treats the input variable as an active row Series, allowing specific column cell access via bracket notation.

**Example:**

```python
    import pandas as pd

    data = {
        'server_id': ['SRV-01', 'SRV-02', 'SRV-03'],
        'cpu_pct': [45, 92, 12],
        'ram_pct': [55, 88, 14]
    }
    df = pd.DataFrame(data)

    # Evaluate both CPU and RAM values inside each row to determine a cluster alert status
    df['alert_status'] = df.apply(
        lambda row: 'CRITICAL' if row['cpu_pct'] > 90 or row['ram_pct'] > 85 else 'NORMAL', 
        axis=1
    )
    print(df)
```

**Output:**

```
      server_id  cpu_pct  ram_pct alert_status
    0    SRV-01       45       55       NORMAL
    1    SRV-02       92       88     CRITICAL
    2    SRV-03       12       14       NORMAL
```

---

## 3. Categorical Dictionary Mapping (`.map()`)

When an entire column vector of text strings or status flags needs to be converted into a completely different set of fixed categorical values, the `.map()` method matches values against an explicit translation dictionary.

**Example:**

```python
    import pandas as pd

    data = {
        'tx_id': [5001, 5002, 5003, 5004],
        'status_code': ['S', 'F', 'S', 'P']
    }
    df = pd.DataFrame(data)

    # Establish an explicit translation registry dictionary
    status_dictionary = {
        'S': 'Success',
        'F': 'Failed',
        'P': 'Pending'
    }

    # Transform the status codes into readable reporting string descriptors
    df['status_desc'] = df['status_code'].map(status_dictionary)
    print(df)
```

**Output:**

```
       tx_id status_code status_desc
    0   5001           S     Success
    1   5002           F      Failed
    2   5003           S     Success
    3   5004           P     Pending
```

> **⚠️ Critical Operational Rule:** The `.map()` method is highly strict. If a cell contains a value that is **not explicitly declared as a key** inside the translation dictionary, Pandas will automatically overwrite that cell with `NaN`.
