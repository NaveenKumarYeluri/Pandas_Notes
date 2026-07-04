# Pandas Core Operations (ETL Transforms)

Once a dataset is loaded into a DataFrame, data engineers must be able to navigate the grid structure, isolate specific subsets, and clean data values before ingestion into analytical stores.

---

## 1. Column Selection (Isolating Subsets)

Pandas treats column extraction differently depending on whether a single attribute or a multi-column subset is requested.

    DataFrame (2D Grid) ───> Extract 1 Column  ───> Pandas Series (1D Array)
                        ───> Extract 2+ Columns ───> New DataFrame (2D Grid)

### Pulling a Single Column (Returns a Series)

Using a single pair of square brackets with the column name as a string returns a 1D Series.

```
    # Extracts the 'city' column as a Pandas Series
    cities = df['city']
```

**Example:**

```python
    import pandas as pd

    data = {
            'id': [101, 102, 103],
            'city': ['Mumbai', 'Delhi', 'Noida']
        }
    df = pd.DataFrame(data)
    print(df['city'])
```

**Output:**

```
    0    Mumbai
    1     Delhi
    2     Noida
    Name: city, dtype: object
```

### Pulling Multiple Columns (Returns a DataFrame)

Passing a Python list inside the bracket selector returns a new 2D DataFrame.

```
    # Extracts both 'id' and 'city' into a structural subset DataFrame
    subset_df = df[['id', 'city']]
```

**Example:**

```python
    import pandas as pd

    data = {
            'id': [101, 102, 103],
            'city': ['Mumbai', 'Delhi', 'Noida']
        }
    df = pd.DataFrame(data)
    print(df[['id', 'city']])
```

**Output:**

```
        id    city
    0  101  Mumbai
    1  102   Delhi
    2  103   Noida
```

---

## 2. Row Filtering (Boolean Masking)

Filtering rows relies on conditional evaluations. Pandas checks every row coordinate against a specified condition, assigns a temporary boolean mask (True or False), and drops the rows evaluated as False.

### Syntax Pattern

```
    # Evaluates the 'id' column and returns rows matching exactly 101
    filtered_df = df[df['id'] == 101]
```

**Example:**

```python
    import pandas as pd

    data = {
            'id': [101, 102, 103],
            'city': ['Mumbai', 'Delhi', 'Noida']
        }
    df = pd.DataFrame(data)
    print(f"first:\n{df['id'] == 101}\n")   # Returns True/False
    print(f"second:\n{df[df['id'] == 101]}\n")   # Outputs matching row/rows
    print(f"third:\n{df['id'].isin([101, 105])}")    # Returns True/False
    # first and third are same except, isin() work as IN operator in SQL.
```

**Output:**

```
    first:
    0     True
    1    False
    2    False
    Name: id, dtype: bool

    second:
        id    city
    0  101  Mumbai

    third:
    0     True
    1    False
    2    False
    Name: id, dtype: bool
```

### Common Conditional Evaluation Operators

* **==** : Equals
* **!=** : Not Equal
* **>** / **<** : Greater than / Less than
* **.isin([val1, val2])** : Evaluates if a cell matches any value within a specified list

### Combining Multiple Conditions (Logical Operators)

When combining multiple conditional filters, standard Python keywords (`and`, `or`) cannot be used. Pandas requires bitwise operators to evaluate entire column vectors simultaneously. 

Additionally, **every individual condition must be enclosed in parentheses `()`** to enforce the correct order of evaluation.

* **`&`** : Logical AND (Both conditions must be True)
* **`|`** : Logical OR (At least one condition must be True)
* **`~`** : Logical NOT (Inverts the condition)

**Example:**

```python
    import pandas as pd

    data = {
        'id': [101, 102, 103],
        'city': ['Mumbai', 'Delhi', 'Noida'],
        'status': ['Active', 'Inactive', 'Active']
    }
    df = pd.DataFrame(data)

    # Filter for rows where city is 'Mumbai' AND status is 'Active'
    combined_filter = df[(df['city'] == 'Mumbai') & (df['status'] == 'Active')]
    print(combined_filter)
```

**Output:**

```
        id    city  status
    0  101  Mumbai  Active
```

---

## 3. Modifying Existing Columns

To clean, standardize, or alter data type states, calculations are applied directly to the column vector and assigned back to the target column identifier.

### String Standardization Example

```
    # Converts all string values in the 'city' column to lowercase text
    df['city'] = df['city'].str.lower()
```

**Example:**

```python
    import pandas as pd

    data = {
            'id': [101, 102, 103],
            'city': ['Mumbai', 'Delhi', 'Noida']
        }
    df = pd.DataFrame(data)
    print(df['city'].str.lower())
```

**Output:**

```
    0    mumbai
    1     delhi
    2     noida
    Name: city, dtype: object
```

### Data Type Casting Example

```
    # Casts integer values to string objects for distinct categorical tracking
    df['id'] = df['id'].astype(str)
```

**Example:**

```python
    import pandas as pd

    data = {
            'id': [101, 102, 103],
            'city': ['Mumbai', 'Delhi', 'Noida']
        }
    df = pd.DataFrame(data)
    print(f"Before:\n{df.dtypes}\n")
    df['id'] = df['id'].astype(str)
    print(f"After:\n{df.dtypes}\n")
```

**Output:**

```
    Before:
    id       int64
    city    object
    dtype: object

    After:
    id      object
    city    object
    dtype: object
```

---

## 4. Adding New Columns (Feature Derivation)

Adding a column uses the exact same syntax pattern as modification, but a brand-new column identifier is passed into the square brackets.

### Static Value Assignment

```
    # Appends a column where every row coordinate defaults to the string 'Active'
    df['status'] = 'Active'
```

**Example:**

```python
    import pandas as pd

    data = {
            'id': [101, 102, 103],
            'city': ['Mumbai', 'Delhi', 'Noida']
        }
    df = pd.DataFrame(data)
    df['status'] = 'Active'     # Static Value assignment
    print(df)
```

**Output:**

```
        id    city  status
    0  101  Mumbai  Active
    1  102   Delhi  Active
    2  103   Noida  Active
```

### Computed Vector Assignment

```
    # Generates a new column by mathematically altering an existing column vector
    df['adjusted_id'] = df['id'] + 5000
```

**Example:**

```python
    import pandas as pd

    data = {
            'id': [101, 102, 103],
            'city': ['Mumbai', 'Delhi', 'Noida']
        }
    df = pd.DataFrame(data)
    df['adjusted_id'] = df['id'] + 5000     # Computed Vector assignment
    print(df)
```

**Output:**

```
        id    city  adjusted_id
    0  101  Mumbai         5101
    1  102   Delhi         5102
    2  103   Noida         5103
```

## 5. Basic Data Sanitization Techniques

Analytical workflows require consistent structural shapes. Missing parameters must be filled and specific subsets isolated before ingestion.

### Overwriting Missing Values (`.fillna()`)

The `.fillna()` method targets missing data points (`NaN`) within a specific column vector and replaces them with a standardized default parameter.

**Example:**

```python
    import pandas as pd

    # Row-oriented dataset missing an entry
    data = [
        {'id': 101, 'city': 'Mumbai'},
        {'id': 102}  # Missing 'city' key parses as NaN
    ]
    df = pd.DataFrame(data)

    # Fill the missing cell coordinate with a default fallback string
    df['city'] = df['city'].fillna('Unknown')
    print(df)
```

**Output:**

```
    id     city
    0  101   Mumbai
    1  102  Unknown
```

## 6. Combined Filtering & Projection (Partition Slicing)

Data partitioning extracts a specific segment of rows while limiting the output footprint to a defined array of structural columns.

**Example:**

```python
    import pandas as pd

    data = {
        'id': [101, 102, 103],
        'city': ['Mumbai', 'Delhi', 'Noida'],
        'status': ['Active', 'Inactive', 'Active']
    }
    df = pd.DataFrame(data)

    # Isolate 'Active' rows, but display ONLY the 'id' and 'city' columns
    active_subset = df[df['status'] == 'Active'][['id', 'city']]
    print(active_subset)
```

**Output:**

```
    id    city
    0  101  Mumbai
    2  103   Noida
```
