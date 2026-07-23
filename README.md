# Data Engineering Practice & Curriculum Notes: Pandas

This repository contains a comprehensive, hands-on data engineering workbook series focused on high-performance data manipulation, memory optimization, and data refining pipelines using Python and Pandas.

---

## 📌 Repository Structure

The workspace is organized into structured theoretical guides, cheat sheets, and practical challenge files:

### Reference & Cheat Sheets

* `0_pandas_syntax_cheatsheet.md` — Complete API reference & code patterns
* `0_pandas_syntax_hierarchy.md` — 3 Levels of Control breakdown (pd vs df vs series)
* `10b_json_orientations.md` — JSON output layout reference cheatsheet

### Theory & Concepts Guides

* `1_basic.md` — Guide: Fundamental Data Structures & Ingestion
* `2_operations.md` — Guide: Core ETL Transforms, Selection & Filtering
* `3_aggregations.md` — Guide: Summary Statistics & GroupBy Mechanics
* `4_combining_datasets.md` — Guide: Joins, Concatenation & Memory Alignment
* `5_datetime_processing.md` — Guide: Datetime Parsing & Time-Series Basics
* `6_advanced_transformations.md` — Guide: Custom `.apply()` & Mapping Operations
* `7_string_manipulation.md` — Guide: Vectorized `.str` Accessors
* `8_structural_deletion.md` — Guide: Feature Dropping & Null Purging
* `9_datatype_casting.md` — Guide: Type Auditing & `.astype()` Conversions
* `10_file_io_operations.md` — Guide: File Exporting (`.to_csv`, `.to_json`)
* `11_advanced_aggregations.md` — Guide: Multi-Metric `.agg()` & Reshaping Pivots
* `12_relational_joins.md` — Guide: Relational Merging Engine (`pd.merge`)
* `13_data_concatenation.md` — Guide: Stacking Data Vertically & Horizontally
* `14_datetime_processing.md` — Guide: Vectorized Time Processing & Timedeltas
* `15_window_shift_rolling_functions.md` — Guide: Shifts, Rolling Windows & Cumulative Totals
* `16_performance_optimization.md` — Guide: Vectorization (`np.where`) & Category Tuning
* `17_string_mining_regex.md` — Guide: Regex Substring Capture & Text Scrubbing
* `18_numerical_ranking.md` — Guide: Vector Ranking (`.rank`) & Window Ordering
* `19_conditional_masking.md` — Guide: Preservation (`.where`), Masking (`.mask`) & Clipping (`.clip`)
* `20_exploding_nested_arrays.md` — Guide: Unpacking Lists & Delimited Strings (`.explode`)

### Practice Challenges

* `1_basics_operations_challenges.md` — Challenges: Basic Structures & Ingestion
* `2_aggregations_challenges.md` — Challenges: Summary Statistics & Grouping
* `3_combining_datasets_challenges.md` — Challenges: Joins & Concatenation
* `4_datetime_challenges.md` — Challenges: Chronological Parsing & Filtering
* `5_advance_transformations_challenges.md` — Challenges: Row-Wise Transformations & Mapping
* `6_string_challenges.md` — Challenges: Text Slicing & Substring Manipulations
* `7_deletion_challenges.md` — Challenges: Structural Drops & Null Purging
* `8_datatype_challenges.md` — Challenges: Type Auditing & Multi-Column Casting
* `9_file_operations_challenges.md` — Challenges: File Serialization & Output Formatting
* `10_advanced_aggregations_challenges.md` — Challenges: Multi-Metric Rollups & Pivoting
* `11_relational_joins_challenges.md` — Challenges: Relational Merges & Asymmetric Schemas
* `12_data_concatination_challenges.md` — Challenges: Vertical Stacking & Bulk Consolidation
* `13_datetime_processing_challenges.md` — Challenges: Latency Calculations & Timedeltas
* `14_window_shift_rolling_functions_challanges.md` — Challenges: Row Shifting & Rolling Analytics
* `15_performance_optimization_challenges.md` — Challenges: Memory Footprint Diagnostics & `np.where`
* `16_string_mining_regex_challenges.md` — Challenges: Unstructured Log Mining & Regex Capture
* `17_numerical_ranking_challenges.md` — Challenges: Positional Priority & Tie-Breaking
* `18_conditional_masking_challenges.md` — Challenges: Element Masking, Preservation & Clipping
* `19_exploding_nested_arrays_challenges.md` — Challenges: List Exploding & Array Unpacking

---

## 🎯 Curriculum Overview

### 1. Architectural Syntax Hierarchy

* **Global Functions (`pd.func`):** Merging (`pd.merge`), stacking (`pd.concat`), and global date parsing (`pd.to_datetime`).
* **Table-Level Methods (`df.method`):** Aggregations (`.groupby`), pivoting (`.pivot_table`), exploding (`.explode`), and memory diagnostics (`.memory_usage`).
* **Vector-Level Methods (`df['col'].method`):** String operations (`.str`), time accessors (`.dt`), numerical ranking (`.rank`), and masking (`.mask`/`.clip`).

### 2. Time Series & Window Analytics

* Row shifting (`.shift()`) for inter-row latency deltas.
* Moving averages and sliding windows (`.rolling()`).
* Cumulative running sums (`.cumsum()`) and historical peak tracking (`.cummax()`).

### 3. Performance & Memory Optimization

* Eliminating slow row loops (`.apply()`) using C-compiled vectorization (`np.where()`).
* Memory compression using categorical data types (`astype('category')`) for low-cardinality strings.

### 4. Unstructured Text Mining (Regex)

* Substring capture group extraction (`.str.extract()`).
* Dynamic text pattern replacements (`.str.replace(..., regex=True)`).

### 5. Advanced Data Manipulation & Normalization

* Tie-breaking strategies with positional ranking (`.rank(method='dense')`).
* Enforcing numeric ceilings and floors using boundary truncation (`.clip()`).
* Unpacking embedded JSON arrays and delimited strings into relational rows (`.str.split()` + `.explode()`).

---

## 🛠️ How to Use This Workbook

1. **Review Notes:** Read through the specific guide file (e.g., `15_performance_optimization.md`) to understand underlying concepts and syntax patterns.
2. **Execute Challenges:** Open the corresponding challenge markdown (e.g., `15_performance_optimization_challenges.md`), implement the code locally in Python, and verify output matrices.
3. **Reference Sheets:** Use `0_pandas_syntax_cheatsheet.md` as an active lookup guide during data engineering pipeline construction.
