# Pandas Fundamental Data Structures

Before building data pipelines, understanding the two fundamental building blocks of Pandas is essential: the **Series** and the **DataFrame**.

* Pandas Series (1D Array)    ───>  A single column of data with an index.
* Pandas DataFrame (2D Grid)  ───>  A collection of Series sharing the same index.

---

## 1. Core Anatomy of a DataFrame
A DataFrame is a 2-dimensional labeled data structure with three core components:
<br>

| Index | id | city |
| :--- | :--- | :--- |
| **0** | 101 | Mumbai |
| **1** | 102 | Delhi |
| **2** | 103 | Noida |

* **The Index:** The horizontal row labels (defaults to `0, 1, 2...` if not specified). It acts like a row address.
* **The Columns:** The vertical labels representing your data fields.
* **The Cells:** The actual data values stored at the intersection of a row and a column.

### Anatomy Example

| Component Type | Index Label | Column: `id` | Column: `city` |
| :--- | :--- | :--- | :--- |
| **Row 0** | `0` | 101 | Mumbai *(Data Cell)* |
| **Row 1** | `1` | 102 | Delhi |
| **Row 2** | `2` | 103 | Noida |

---

## 1.5. Basic DataFrame Creation in Python
Before utilizing any inspection properties, the DataFrame must be initialized in memory. The standard initialization syntax requires importing the library and passing a compatible data structure (such as a dictionary) directly into the constructor.

```python
    # Step 1: Import the Pandas library (aliased as 'pd' by convention)
    import pandas as pd

    # Step 2: Define your raw data structure
    data = {
        'id': [101, 102, 103],
        'city': ['Mumbai', 'Delhi', 'Noida']
    }

    # Step 3: Instantiate the DataFrame object
    df = pd.DataFrame(data)
    
    # Step 4: Check output
    print(df)
```
**Output:**
<br>

|       | id   | city   |
| :---  | :--- | :---   |
| **0** | 101  | Mumbai |
| **1** | 102  | Delhi  |
| **2** | 103  | Noida  |

---

## 2. Structural Inspection Properties
In data engineering pipelines, the primary step after loading any dataset is inspecting its shape and data types (`dtypes`). Pandas provides three basic properties to complete this check instantly without altering the underlying data.

### Checking Table Shape (`df.shape`)
Returns a tuple containing the number of rows and columns `(rows, columns)`.

```python
    import pandas as pd

    data = {
        'id': [101, 102, 103],
        'city': ['Mumbai', 'Delhi', 'Noida']
    }
    df = pd.DataFrame(data)
    
    print(df.shape)
```
**Output:**

(3, 2)

### Viewing Data Types (`df.dtypes`)
This property returns the data type of each independent column. It is used to verify if columns parsed correctly (e.g., numbers read as integers or floats rather than text objects).

```python
    import pandas as pd

    data = {
        'id': [101, 102, 103],
        'city': ['Mumbai', 'Delhi', 'Noida']
    }
    df = pd.DataFrame(data)
    
    print(df.dtypes)
```
**Output:**

    id       int64
    city    object
    dtype: object
    
> **💡 Understanding the Output:**
> When running `df.dtypes`, the final line `dtype: object` represents the data type of the output Series object itself, because Pandas returns the column data types as a text-based Series. It does not indicate that the underlying dataset columns are broken.

### Getting the Full Summary (`df.info()`)
This method prints a comprehensive structural breakdown of the DataFrame. It shows you the memory footprint, non-null counts, and data types all in one place.

```python
    import pandas as pd

    data = {
        'id': [101, 102, 103],
        'city': ['Mumbai', 'Delhi', 'Noida']
    }
    df = pd.DataFrame(data)
    
    df.info()
```
**Output:**

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3 entries, 0 to 2
    Data columns (total 2 columns):
    #   Column  Non-Null Count  Dtype 
    ---  ------  --------------  ----- 
    0   id      3 non-null      int64 
    1   city    3 non-null      object
    dtypes: int64(1), object(1)
    memory usage: 180.0+ bytes
    None

The breakdown includes:

* The exact count of rows and columns.
* A list of all columns and their data types.
* The count of **Non-Null** values (instantly showing you if data is missing).
* The total RAM memory size used by the DataFrame.

---

## 3. Pandas Ingestion & Structural Mapping Guide

### 1. Dictionary of Lists (Column-Oriented Mapping)
When passing a dictionary of lists, Pandas constructs the table **column by column**. Each key maps to an independent vertical vector.

#### Raw Data Input

```python
    import pandas as pd

    data = {
        'id': [101, 102, 103],
        'city': ['Mumbai', 'Delhi', 'Noida']
    }
    df = pd.DataFrame(data)
```
**Output:**

| Index | id | city |
| :--- | :--- | :--- |
| **0** | 101 | Mumbai |
| **1** | 102 | Delhi |
| **2** | 103 | Noida |

> **⚠️ The Length Error Rule:** If `'city'` had a 4th element `['Mumbai', 'Delhi', 'Noida', 'Goa']`, alignment with the 3-element `'id'` column fails because the structure cannot safely balance mismatched vectors. It terminates immediately with `ValueError: All arrays must be of the same length`.

---

### 2. List of Dictionaries (Row-Oriented Mapping)

When passing a list of dictionaries, Pandas constructs the table row by row. Each dictionary block within the array is processed as a complete horizontal record.

#### Raw Data Input

```python
    import pandas as pd

    data = [
        {'id': 101, 'city': 'Mumbai'},
        {'id': 102, 'city': 'Delhi'},
        {'id': 103}  # Missing the 'city' key entirely
    ]
    df = pd.DataFrame(data)
```
**Output:**

| Index | id | city |
| :--- | :--- | :--- |
| **0** | 101 | Mumbai |
| **1** | 102 | Delhi |
| **2** | 103 | `NaN` |

> **💡 The Auto-Fill Rule:** If a specific row dictionary lacks a key found elsewhere in the schema, Pandas automatically appends a `NaN`(Not a Number) placeholder to that cell coordinate to preserve the 2D grid structure.
