# Pandas Core Architecture Syntax Cheatsheet

This single-file document serves as a high-density reference sheet for syntax mapping, core operations, and structural data engineering patterns.

---

## 1. Vector Manipulation, Selection & Regex Mining

### Select Columns

* **Syntax:** `df[['col1', 'col2']]`
* **Explanation:** Filters the DataFrame horizontally to extract a targeted subset of column vectors.
* **Example:** `df_users = df[['account_handle', 'email']]`

### Element Vector Slicing (`.str.contains()`)

* **Syntax:** `df['column'].str.contains('pattern')`
* **Explanation:** Generates a boolean mask tracking if text fields contain a specific substring pattern.
* **Example:** `mask = df['system_code'].str.contains('REJECT')`

### The Tilde Inversion Mask Operator (`~`)

* **Syntax:** `df[~mask]`
* **Explanation:** Inverts a boolean condition array to filter out specific target records.
* **Example:** `df_clean = df[~mask]`

### Vectorized String Trimming & Casing (`.str.strip()` / `.str.upper()`)

* **Syntax:** `df['column'].str.strip().str.upper()`
* **Explanation:** Strips leading/trailing whitespaces and transforms text elements into uppercase.
* **Example:** `df['region_tag'] = df['region_tag'].str.strip().str.upper()`

### Regular Expression Capture Extraction (`.str.extract()`)

* **Syntax:** `df['column'].str.extract(r'pattern_with_(capture_groups)')`
* **Explanation:** Uses regex capture groups `()` to mine and split unstructured text patterns directly into distinct columns.
* **Example:** `df[['level', 'code']] = df['payload'].str.extract(r'\[([A-Z]+)\] code:(\d+)')`

### Regex Pattern Replacement (`.str.replace()`)

* **Syntax:** `df['column'].str.replace(r'pattern', 'replacement', regex=True)`
* **Explanation:** Dynamically matches complex text patterns and strips or replaces them concurrently.
* **Example:** `df['clean_tag'] = df['raw_tag'].str.replace(r'\s\[v\d+\]', '', regex=True)`

---

## 2. Missing Value Management, Performance & Memory Tuning

### Localized Null Purging (`.dropna()`)

* **Syntax:** `df.dropna(subset=['column'])`
* **Explanation:** Discards rows missing records within a specific column variable list subset.
* **Example:** `df = df.dropna(subset=['timestamp_raw'])`

### Single-Column & Bulk Vector Conversion (`.astype()`)

* **Syntax:** `df['column'].astype('datatype')`
* **Explanation:** Recasts a column's underlying memory structure explicitly into a new primitive or categorical data type.
* **Example:** `df['cost'] = df['cost_str'].astype('float64')`

### High-Speed Vectorized Conditionals (`np.where()`)

* **Syntax:** `np.where(condition, value_if_true, value_if_false)`
* **Explanation:** Evaluates logic concurrently across an entire vector at the C-layer, bypassing slow Python row loops (`.apply()`).
* **Example:** `df['status'] = np.where(df['response'] >= 400, 'ERROR', 'SUCCESS')`

### Categorical Memory Compression (`astype('category')`)

* **Syntax:** `df['column'] = df['column'].astype('category')`
* **Explanation:** Replaces repetitive, low-cardinality string columns with an integer dictionary lookup, reducing RAM overhead by up to 90%.
* **Example:** `df['region'] = df['region'].astype('category')`

### Memory Footprint Diagnostics (`.memory_usage()`)

* **Syntax:** `df.memory_usage(deep=True)`
* **Explanation:** Returns the exact memory footprint allocated by each column in bytes.
* **Example:** `bytes_used = df.memory_usage(deep=True)['region_tag']`

### Drop Columns (`.drop()`)

* **Syntax:** `df.drop(columns=['column_name'])`
* **Explanation:** Prunes administrative or unnecessary feature tracking keys horizontally from the data grid.
* **Example:** `df = df.drop(columns=['internal_token'])`

---

## 3. Element Masking, Outlier Clipping & Array Unpacking

### Boolean Preservation & Overwriting (`.where()` / `.mask()`)

* **Syntax:** `df['col'].where(cond)` (Keeps `True`) | `df['col'].mask(cond, other)` (Replaces `True`)
* **Explanation:** Replaces or preserves values matching boolean conditions without altering unflagged rows.
* **Example:** `df['status'] = df['status'].mask(df['status'] >= 900, -1)`

### Boundary Truncation (`.clip()`)

* **Syntax:** `df['col'].clip(lower=floor_val, upper=ceiling_val)`
* **Explanation:** Truncates numeric outliers strictly between a specified minimum floor and maximum ceiling.
* **Example:** `df['latency_ms'] = df['latency_ms'].clip(lower=0.0, upper=500.0)`

### Delimited String Splitting (`.str.split()`)

* **Syntax:** `df['col'].str.split('delimiter')`
* **Explanation:** Splits text strings containing delimiters into lists of individual string elements.
* **Example:** `df['tags_list'] = df['tags_str'].str.split(',')`

### Unpacking Nested Arrays (`.explode()`)

* **Syntax:** `df.explode('column_name')`
* **Explanation:** Replicates row metadata and expands each item of an embedded list into its own distinct relational row.
* **Example:** `df_normalized = df.explode('tags_list').reset_index(drop=True)`

---

## 4. Time Series, Window Analytics & Numerical Ranking

### String-to-Timestamp Parsing (`pd.to_datetime()`)

* **Syntax:** `pd.to_datetime(df['column'], errors='coerce')`
* **Explanation:** Converts text dates into timestamp arrays, forcing unparseable text noise into `NaT`.
* **Example:** `df['start_time'] = pd.to_datetime(df['start_str'], errors='coerce')`

### Row Shifting (`.shift()`)

* **Syntax:** `df['column'].shift(periods=1)` or `df.groupby('group')['col'].shift(1)`
* **Explanation:** Shifts column vectors up or down to compare current row metrics against preceding/subsequent rows.
* **Example:** `df['prev_time'] = df.groupby('node')['timestamp'].shift(1)`

### Moving Window Calculations (`.rolling()`)

* **Syntax:** `df['column'].rolling(window=size, min_periods=1).mean()`
* **Explanation:** Creates a sliding row window to compute rolling statistics like moving averages.
* **Example:** `df['rolling_avg'] = df['load'].rolling(window=3, min_periods=1).mean()`

### Running Cumulative Totals (`.cumsum()` / `.cummax()`)

* **Syntax:** `df['column'].cumsum()` / `df['column'].cummax()`
* **Explanation:** Maintains an ongoing mathematical running tally or records historical peaks down rows.
* **Example:** `df['running_total'] = df['cost'].cumsum()`

### Positional Numerical Ranking (`.rank()`)

* **Syntax:** `df['column'].rank(method='dense', ascending=False)`
* **Explanation:** Assigns numerical positions to row values, using strategies like `dense`, `min`, or `first` to manage ties.
* **Example:** `df['rank'] = df.groupby('cluster')['cost'].rank(method='dense', ascending=False)`

---

## 5. Advanced Aggregations, Reshaping & Serialization

### Multi-Metric Rollups (`.groupby().agg()`)

* **Syntax:** `df.groupby('group_col', observed=True).agg(new_metric=('target_col', 'function'))`
* **Explanation:** Executes multiple custom aggregations per group (using `observed=True` for categorical efficiency).
* **Example:** `df.groupby('region', observed=True).agg(avg_lat=('latency', 'mean'), total=('tx_id', 'count'))`

### Reshaping Data Layouts (`.pivot_table()`)

* **Syntax:** `df.pivot_table(index='row_col', columns='hdr_col', values='num_col', aggfunc='sum', fill_value=0.0, observed=True)`
* **Explanation:** Rotates distinct categorical values horizontally into header columns while aggregating intersections.
* **Example:** `df.pivot_table(index='tx_id', columns='node', values='cost', fill_value=0.0, aggfunc='sum', observed=True)`

### Flat Tabular & JSON Serialization (`.to_csv()` / `.to_json()`)

* **Syntax:** `df.to_csv('file.csv', index=False)` | `df.to_json('file.json', orient='records', indent=4)`
* **Explanation:** Writes active DataFrames directly to disk as flat CSVs or nested JSON records.
* **Example:** `df.to_csv('warehouse_report.csv', index=False)`

---

## 6. Relational Lookups & Stacking Combinations

### The Relational Joining Engine (`pd.merge()`)

* **Syntax:** `pd.merge(left_df, right_df, on='shared_key', how='left')`
* **Explanation:** Combines distinct tables horizontally based on matching keys, mimicking SQL joins.
* **Example:** `df_enriched = pd.merge(df_tx, df_regions, on='region_id', how='left')`

### Vertical Telemetry Stacking (`pd.concat(axis=0)`)

* **Syntax:** `pd.concat([df1, df2], axis=0).reset_index(drop=True)`
* **Explanation:** Stacks sequential incremental batch files vertically top-to-bottom into a single tracking matrix.
* **Example:** `df_daily = pd.concat([df_part_a, df_part_b], axis=0).reset_index(drop=True)`
