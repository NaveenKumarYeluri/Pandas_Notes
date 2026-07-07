# Pandas Datetime Processing and Time-Series Alignment

Processing temporal fields is a critical phase of structural sanitization. Transaction tables, application logs, and sensor tracking streams routinely record timestamps as raw string text. To execute time-based sorting, periodic grouping, or date arithmetic, these strings must be parsed into proper datetime objects.

---

## 1. Converting Text Strings to Datetime (`pd.to_datetime()`)

When data is loaded from JSON payloads or CSV text files, date columns default to standard text objects (`object` or `string` data types). The `pd.to_datetime()` function scans the target column vector, parses the text layout, and casts the elements into concrete `datetime64[ns]` formats.

### Syntax Architecture

```python
    df['timestamp_column'] = pd.to_datetime(df['string_column'])
```

**Example**

```python
    import pandas as pd

    data = {
        'tx_id': [801, 802, 803],
        'created_at': ['2026-07-01 10:15:00', '2026-07-02 11:30:22', '2026-07-03 14:05:10']
    }
    df = pd.DataFrame(data)

    print("--- Before Conversion ---")
    print(df.dtypes)

    # Cast the text objects into true datetime metrics
    df['created_at'] = pd.to_datetime(df['created_at'])

    print("\n--- After Conversion ---")
    print(df.dtypes)
```

**Output:**

```
--- Before Conversion ---
tx_id          int64
created_at    object
dtype: object

--- After Conversion ---
tx_id                  int64
created_at    datetime64[ns]
dtype: object
```

## 2. Extracting Specific Time Components (`.dt` Properties)

Once a column has been transformed into a `datetime64[ns]` data type, Pandas unlocks a powerful vector accessor namespace called `.dt`. This namespace permits the direct, element-wise extraction of sub-temporal properties across the entire column vector without requiring complex loops.

**Common Core Temporal Extractors**

* `.dt.year`: Extracts the 4-digit calendar year index.

* `.dt.month`: Extracts the numeric calendar month (1 to 12).

* `.dt.day`: Extracts the day of the month (1 to 31).

* `.dt.hour`: Extracts the 24-hour timestamp block (0 to 23).

* `.dt.day_name()`: Returns the specific weekday text string (e.g., 'Monday', 'Tuesday').

**Example**

```python
    import pandas as pd

    data = {
        'log_id': [501, 502],
        'timestamp': pd.to_datetime(['2026-01-15 08:30:00', '2026-07-07 18:45:12'])
    }
    df = pd.DataFrame(data)

    # Derive categorical dimensions for downstream data warehouse queries
    df['extracted_year'] = df['timestamp'].dt.year
    df['extracted_month'] = df['timestamp'].dt.month
    df['weekday'] = df['timestamp'].dt.day_name()

    print(df)
```

**Output**

```
   log_id           timestamp  extracted_year  extracted_month    weekday
0     501 2026-01-15 08:30:00            2026                1   Thursday
1     502 2026-07-07 18:45:12            2026                7    Tuesday
```

## 3. Filtering Rows Using Date Boundaries

Filtering records based on chronological time windows uses the exact same boolean indexing patterns used for numeric masks. Datetime objects accept standard comparison operators (`>`, `<`, `==`, `>=`).

**Example**

```python
    import pandas as pd

    data = {
        'tx_id': [1001, 1002, 1003],
        'date': pd.to_datetime(['2026-06-15', '2026-07-02', '2026-07-06'])
    }
    df = pd.DataFrame(data)

    # Define a strict operational cutoff window boundary string
    # Pandas automatically parses the string to execute the vector mask
    h2_cutoff_filter = df[df['date'] >= '2026-07-01']
    print(h2_cutoff_filter)
```

**Output**

```
   tx_id       date
1   1002 2026-07-02
2   1003 2026-07-06
```

## 4. Time-Series Aggregations (Temporal GroupBy)

By combining date property extraction blocks with the segmented `.groupby()` architectures studied in the previous module, data engineers can summarize high-frequency transaction rows into periodic business metrics (such as calculating total monthly revenue streams).

**Example**

```python
    import pandas as pd

    data = {
        'transaction_date': pd.to_datetime(['2026-01-10', '2026-01-22', '2026-02-05', '2026-02-18']),
        'amount': [1500, 3200, 4500, 1200]
    }
    df = pd.DataFrame(data)

    # Step 1: Isolate the month index vector as a temporary grouping attribute
    df['tx_month'] = df['transaction_date'].dt.month

    # Step 2: Group rows by month and sum the numeric values
    monthly_financials = df.groupby('tx_month')['amount'].sum().reset_index()
```

**Output:**

```
   tx_month  amount
0         1    4700
1         2    5700
```
