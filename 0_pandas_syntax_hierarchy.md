# Pandas Syntax Hierarchy: Understanding the 3 Levels of Control

When working with Pandas, syntax confusion usually happens because the library operates at three distinct levels of control. Grouping methods by **where they live** makes it much easier to remember how to write them.

---

## Level 1: Global Functions (`pd.method`)

These are top-level utility functions belonging directly to the imported Pandas package. You call these when you are passing in whole DataFrames, series, or lists of data as parameters to generate or parse something completely new.

* **Merging Tables:** `pd.merge(df_left, df_right, on='shared_id', how='left')`
  * *Why:* You are blending two independent tables horizontally.
* **Stacking Tables:** `pd.concat([df_a, df_b], axis=0, ignore_index=True)`
  * *Why:* You are taking a Python list of separate tables and combining them.
* **Chronological Ingestion:** `pd.to_datetime(df['date_str'], errors='coerce')`
  * *Why:* You are passing a raw text array into the global parser engine to convert it.

---

## Level 2: Table-Level Methods (`df.method`)

These actions are called directly on an existing, instantiated DataFrame variable. They modify or analyze the entire structural matrix grid—affecting rows, column schemas, shapes, or file outputs.

* **Grouping & Aggregations:** `df.groupby('category').agg(...)`
  * *Why:* You are collapsing the entire vertical distribution of the table grid.
* **Matrix Reshaping:** `df.pivot_table(index='row', columns='hdr', values='num')`
  * *Why:* You are completely rotating the dimensional axes of the whole table.
* **Dropping Column Keys:** `df.drop(columns=['internal_token'])`
  * *Why:* You are cutting a structural feature track out of the entire database schema grid.
* **Index Normalization:** `df.reset_index(drop=True)`
  * *Why:* You are re-indexing every row coordinate across the whole table.
* **File Serialization:** `df.to_csv('file.csv')` or `df.to_json('file.json')`
  * *Why:* You are converting the entire active memory table layout into a physical file layout.

---

## Level 3: Column-Level / Vectorized Methods (`df['col'].method`)

These are specialized vector operations called on a single vertical column track (a Pandas `Series`). They extract internal metadata or scrub text/numeric elements line-by-line using high-speed underlying arrays.

* **Memory Data Type Casting:** `df['cost_str'].astype('float64')`
  * *Why:* You are transforming the data storage format of just that single vector.
* **Vectorized Text Filtering:** `df['code'].str.contains('REJECT')`
  * *Why:* You are looking inside the string text elements of one specific column.
* **Whitespace Trimming:** `df['node'].str.strip()`
  * *Why:* You are cleaning up formatting issues inside that specific text field.
* **Case Normalization:** `df['tag'].str.upper()`
  * *Why:* You are altering the letter casing within that single text vector.
* **Chronological Accessors:** `df['timestamp'].dt.hour` or `df['duration'].dt.total_seconds()`
  * *Why:* You are diving into the `.dt` property namespace unique to that specific datetime series.

---

## Summary Cheat-Rule 💡

| If your target goal is to... | ...then use this syntax branch: | Quick Mental Check |
| :--- | :--- | :--- |
| **Mix, parse, or combine** multiple separate data layers together | **`pd.function()`** | *"I'm passing data pieces into the global engine."* |
| **Shape, slice, group, or save** the entire horizontal/vertical table | **`df.method()`** | *"I'm changing or exporting the whole data grid."* |
| **Scrub, recast, or inspect** values inside a single vertical lane | **`df['col'].method()`** | *"I'm operating purely on one data vector lane."* |