# Pandas Conditional Element Masking & Clipping

Data cleaning pipelines frequently require enforcing boundaries on numeric metrics (like capping latency spikes) or hiding fields based on criteria. Pandas offers native vectorized filtering engines through `.where()`, `.mask()`, and `.clip()`.

---

## 1. Keep vs. Overwrite Values (`.where()` vs. `.mask()`)

These two methods are exact logical inverses of each other. They allow you to swap out elements that fail or pass a specific boolean test array.

* **`.where(cond, other)`**: **Keeps** values where the condition is True; replaces values where it is False.
* **`.mask(cond, other)`**: **Masks (replaces)** values where the condition is True; keeps values where it is False.

### Implementation Example

```python
    import pandas as pd

    df = pd.DataFrame({'status': [200, 500, 200]})

    # Replace bad codes with -1 using mask (True gets replaced)
    df['sanitized_status'] = df['status'].mask(df['status'] == 500, -1)
    print(df)
```

**Output:**

```
       status  sanitized_status
    0     200               200
    1     500                -1
    2     200               200
```

---

## 2. Enforcing Value Outlier Ceilings and Floors (`.clip()`)

The `.clip()` method truncates values outside a specified lower and upper boundary limit. Any numeric value falling below the floor is bumped up to the floor, and any value exceeding the ceiling is trimmed down to the ceiling.

### Outlier Clipping Example

```python
    import pandas as pd

    df = pd.DataFrame({'cpu_pct': [12, 105, -5, 88]})

    # Clip boundaries strictly between 0% and 100% operational caps
    df['clean_cpu'] = df['cpu_pct'].clip(lower=0, upper=100)
    print(df)
```

**Output:**

```
       cpu_pct  clean_cpu
    0       12         12
    1      105        100
    2       -5          0
    3       88         88
```
