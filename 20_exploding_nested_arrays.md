# Pandas Exploding & Unpacking Nested Arrays (`.explode()`)

Semi-structured staging data (such as JSON API payloads or log tag arrays) frequently lands with lists embedded inside single DataFrame cells. Data engineers use `.explode()` to unpack these nested arrays into normalized tabular rows.

---

## 1. Unpacking Column Lists into Rows (`.explode()`)

The `.explode()` method takes a column containing list-like elements and replicates the index and surrounding metadata for every item inside the list array.

### Core Operational Rules
* **Lists / Arrays:** Each element in the list gets its own row.
* **Empty Lists (`[]`):** Result in a single row with `NaN` in the exploded column.
* **Scalar / Non-List Values:** Retained as-is in a single row.

### Implementation Example

    import pandas as pd

    df = pd.DataFrame({
        'user_id': [101, 102],
        'roles': [['ADMIN', 'DEVELOPER'], ['ANALYST']]
    })

    # Explode the nested list vector into distinct rows
    df_exploded = df.explode('roles').reset_index(drop=True)
    print(df_exploded)

**Expected Output:**

   user_id      roles
0      101      ADMIN
1      101  DEVELOPER
2      102    ANALYST

---

## 2. String-to-List Preprocessing (`.str.split()` + `.explode()`)

If a column contains delimited text strings (e.g., `'AWS|PYTHON|SQL'`) rather than actual Python list objects, you must chain `.str.split()` prior to calling `.explode()`.

### String Unpacking Pipeline Example

    import pandas as pd

    df = pd.DataFrame({
        'task_id': ['T1', 'T2'],
        'skills_required': ['AWS|PYTHON', 'SQL']
    })

    # Step 1: Split delimited text into lists
    df['skills_list'] = df['skills_required'].str.split('|')

    # Step 2: Explode lists into normalized tabular rows
    df_normalized = df.explode('skills_list').drop(columns=['skills_required']).reset_index(drop=True)
    print(df_normalized)

**Expected Output:**

  task_id skills_list
0      T1         AWS
1      T1      PYTHON
2      T2         SQL