# Pandas Syntax Hierarchy: Understanding the 3 Levels of Control

When working with Pandas, syntax confusion usually happens because the library operates at three distinct levels of control. Grouping methods by **where they live** makes it much easier to remember how to write them.

---

## Level 1: Global Functions (`pd.method`)

These are top-level utility functions belonging directly to the imported Pandas package. You call these when you are passing in whole DataFrames, series, or lists of data as parameters to generate or parse something completely new.

* **Merging Tables:** `pd.merge(df_left, df_right, on='shared_id', how='left')`
  * *Why:* You are blending two independent tables horizontally.
* **Stacking Tables:** `pd.concat([df_a, df_b], axis=0, ignore_index=True)`
  * *Why:* You are taking a Python list of separate tables and combining them.
* **Chronological Parsing:** `pd.to_datetime(df['date_str'], errors='coerce')`
  * *Why:* You are passing a raw text array into the global parser engine to convert it to timestamp objects.

---

## Level 2: Table-Level Methods (`df.method`)

These actions are called directly on an existing, instantiated DataFrame variable. They modify or analyze the entire structural matrix grid—affecting rows, column schemas, shapes, or file outputs.

* **Grouping & Aggregations:** `df.groupby('category', observed=True).agg(...)`
  * *Why:* You are collapsing the entire vertical distribution of the table grid.
* **Matrix Reshaping:** `df.pivot_table(index='row', columns='hdr', values='num', aggfunc='sum', fill_value=0.0)`
  * *Why:* You are completely rotating the dimensional axes of the whole table.
* **Unpacking Embedded Lists:** `df.explode('list_column')`
  * *Why:* You are duplicating table metadata and expanding nested arrays into individual rows.
* **Index Normalization:** `df.reset_index(drop=True)`
  * *Why:* You are re-indexing every row coordinate across the whole table.
* **Dropping Feature Columns:** `df.drop(columns=['internal_token'])`
  * *Why:* You are cutting a structural feature track out of the entire database schema grid.
* **Memory Usage Auditing:** `df.memory_usage(deep=True)`
  * *Why:* You are measuring the total system memory allocated by every column in the table.
* **File Serialization:** `df.to_csv('file.csv')` or `df.to_json('file.json')`
  * *Why:* You are converting the active memory table layout into a physical file on disk.

---

## Level 3: Column-Level / Vectorized Methods (`df['col'].method`)

These are specialized vector operations called on a single vertical column track (a Pandas `Series`). They extract internal metadata, execute analytical windows, or scrub text/numeric elements line-by-line using high-speed underlying arrays.

* **High-Speed Vector Conditionals:** `np.where(df['load'] > 80, 'CRITICAL', 'NORMAL')`
  * *Why:* Executes C-layer conditional logic across the column vector without a Python row loop (`.apply()`).
* **Memory & Category Conversion:** `df['status'].astype('category')` or `df['cost'].astype('float64')`
  * *Why:* You are transforming the storage type or compressing strings inside a single vector lane.
* **Regular Expression Mining:** `df['payload'].str.extract(r'code:(\d+)')` or `df['tag'].str.replace(...)`
  * *Why:* You are isolating substrings or altering text patterns inside a specific text vector.
* **Delimited String Splitting:** `df['skills'].str.split('|')`
  * *Why:* You are splitting delimited text elements inside a single column into lists.
* **Row Shifting & Rolling Analytics:** `df['cost'].shift(1)` or `df['cost'].rolling(3).mean()`
  * *Why:* You are computing moving metrics across neighboring rows inside a specific column vector.
* **Cumulative Running Metrics:** `df['cost'].cumsum()` or `df['cost'].cummax()`
  * *Why:* You are keeping a continuous running sum or tracking historical peaks down a single column.
* **Positional Numerical Ranking:** `df['cost'].rank(method='dense', ascending=False)`
  * *Why:* You are assigning relative rank values to elements inside a single data vector.
* **Conditional Masking & Overwriting:** `df['status'].mask(df['status'] >= 900, -1)` or `.where()`
  * *Why:* You are selectively masking or preserving values matching a boolean condition within a single column.
* **Outlier Boundary Truncation:** `df['latency'].clip(lower=0.0, upper=500.0)`
  * *Why:* You are capping numeric outliers strictly between ceiling and floor values in one column.
* **Chronological Accessors:** `df['timestamp'].dt.hour` or `df['duration'].dt.total_seconds()`
  * *Why:* You are accessing properties within the `.dt` namespace unique to datetime series.

---

## Summary Cheat-Rule 💡

| If your target goal is to... | ...then use this syntax branch: | Quick Mental Check |
| :--- | :--- | :--- |
| **Mix, parse, or combine** multiple separate data layers together | **`pd.function()`** | *"I'm passing data pieces into the global engine."* |
| **Shape, slice, group, explode, or save** the entire table grid | **`df.method()`** | *"I'm changing, exploding, or exporting the whole data grid."* |
| **Scrub, rank, clip, mask, or shift** values inside a single vertical lane | **`df['col'].method()`** | *"I'm operating purely on one data vector lane."* |
