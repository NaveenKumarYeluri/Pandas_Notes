# Pandas Numerical Ranking & Window Ordering (`.rank()`)

Data engineering pipelines frequently require ordering records relative to each other—such as prioritizing tasks by execution time, identifying top resource-consuming jobs, or deduplicating streaming logs. Pandas provides the vectorized `.rank()` method to assign numerical positions to row elements based on their data values.

---

## 1. Ranking Methods and Tie-Breaking Strategies

When two or more rows contain identical values, you must define a tie-breaking strategy using the `method` parameter to control how the numerical position is assigned.

### Core Ranking Methods

* **`method='default'` (Average):** Assigns the average rank to all tied elements.
* **`method='min'`:** Assigns the lowest rank in the tie group to all tied elements (like classic sporting ranks).
* **`method='dense'`:** Ranks are incremented by 1 for each unique value group, leaving no structural rank gaps between positions.
* **`method='first'`:** Assigns ranks in the order the elements appear in the DataFrame row hierarchy, breaking ties arbitrarily by position.

### Ranking Direction

* **`ascending=True`:** Smallest values get the lowest rank numbers (rank 1).
* **`ascending=False`:** Largest values get the lowest rank numbers (rank 1).

---

## 2. Global Column Ranking Example

Running `.rank()` on a single standalone column computes positions relative to every single row inside the entire DataFrame vector lane.

### Implementation Script

```python
    import pandas as pd

    df = pd.DataFrame({
        'job_id': [101, 102, 103, 104],
        'runtime_sec': [45.0, 120.0, 45.0, 90.0]
    })

    # Rank the runtimes from shortest to longest using dense ranking for ties
    df['runtime_rank'] = df['runtime_sec'].rank(method='dense', ascending=True)
    print(df)
```

**Output:**

```
       job_id  runtime_sec  runtime_rank
    0     101         45.0           1.0
    1     102        120.0           4.0
    2     103         45.0           1.0
    3     104         90.0           2.0
```

---

## 3. Windowed Partitioned Ranking (`.groupby().rank()`)

In standard cloud analytics, you rarely want to rank an entire database globally. Instead, you usually need to rank records *within a specific partition window*—such as finding the highest-cost transactions inside each regional server cluster independently. Chaining `.groupby()` with `.rank()` resets the ranking scale for every unique group token.

### Windowed Ranking Example

```python
    import pandas as pd

    df = pd.DataFrame({
        'cluster': ['MUM', 'DEL', 'MUM', 'DEL'],
        'task_id': [5001, 5002, 5003, 5004],
        'cost': [12.50, 45.00, 88.20, 15.00]
    })

    # Rank tasks by cost (highest cost gets rank 1) independently inside each regional cluster partition
    df['cost_rank_in_cluster'] = df.groupby('cluster')['cost'].rank(method='dense', ascending=False)
    print(df.sort_values(by=['cluster', 'cost_rank_in_cluster']))
```

**Output:**

```
      cluster  task_id  cost  cost_rank_in_cluster
    1     DEL     5002  45.0                   1.0
    3     DEL     5004  15.0                   2.0
    2     MUM     5003  88.2                   1.0
    0     MUM     5001  12.5                   2.0
```
