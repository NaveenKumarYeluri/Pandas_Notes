# Pandas Vectorized String Manipulation (`.str` Accessors)

When string data is stored inside a Pandas DataFrame column, it defaults to the `object` or `string` data type. To modify or evaluate text elements across an entire column vector efficiently without using slow Python loops, you must use the special **`.str` accessor** namespace.

---

## 1. Modifying Substrings (`.str.replace()`)

The `.str.replace()` method targets specific characters or words inside a text vector and replaces them with an alternative string. This is highly useful for cleaning messy codes, stripping custom symbols, or normalizing prefixes.

**Example:**

```python
    import pandas as pd

    data = {
        'tx_id': [3001, 3002, 3003],
        'legacy_code': ['PROD_MUM_01', 'PROD_DEL_02', 'DEV_MUM_03']
    }
    df = pd.DataFrame(data)

    # Convert the legacy product prefix 'PROD_' to a modernized 'PRD_' handle
    df['cleaned_code'] = df['legacy_code'].str.replace('PROD_', 'PRD_')
    print(df)
```

**Output:**

```
       tx_id  legacy_code cleaned_code
    0   3001  PROD_MUM_01   PRD_MUM_01
    1   3002  PROD_DEL_02   PRD_DEL_02
    2   3003   DEV_MUM_03   DEV_MUM_03
```

---

## 2. Text Substring Filtering (`.str.contains()`)

To filter a table based on whether a text column contains a specific keyword, phrase, or pattern, use `.str.contains()`. This method evaluates each row and returns a boolean vector (`True` or `False`) that can be used directly as a row mask.

**Example:**

```python
    import pandas as pd

    data = {
        'instance_id': ['SRV-01', 'SRV-02', 'SRV-03', 'SRV-04'],
        'cluster_group': ['mumbai-prod-cluster', 'delhi-dev-cluster', 'mumbai-test-cluster', 'noida-prod-cluster']
    }
    df = pd.DataFrame(data)

    # Isolate only the instances that are running in a 'prod' environment
    prod_mask = df['cluster_group'].str.contains('prod')
    df_prod = df[prod_mask]
    
    print(df_prod)
```

**Output:**

```
      instance_id        cluster_group
    0      SRV-01  mumbai-prod-cluster
    3      SRV-04   noida-prod-cluster
```

---

## 3. String Slicing and Splitting (`.str[:n]` & `.str.split()`)

Text data vectors can be chopped or fragmented cleanly based on index positions or specific delimiter characters.

* **Positional Slicing (`.str[start:stop]`):** Extracts text based on exact character positions (similar to standard Python slicing).
* **Delimiter Splitting (`.str.split()`):** Breaks a single text block into a Python list of fragments based on a separator character (like a comma, underscore, or space).

**Example:**

```python
    import pandas as pd

    data = {
        'user_id': [101, 102],
        'region_date': ['MUM_2026-07', 'DEL_2026-08']
    }
    df = pd.DataFrame(data)

    # 1. Positional Slicing: Extract just the 3-letter region code prefix
    df['region_code'] = df['region_date'].str[0:3]

    # 2. Delimiter Splitting: Split by underscore and pull out the date part (index 1)
    df['date_part'] = df['region_date'].str.split('_').str[1]

    print(df)
```

**Output:**

```
       user_id  region_date region_code date_part
    0      101  MUM_2026-07         MUM   2026-07
    1      102  DEL_2026-08         DEL   2026-08
```

---

## 4. Comprehensive Vectorized String Methods Reference

The `.str` accessor space provides dozens of built-in vectorized methods to manipulate text columns cleanly. Below is the structural checklist of core string transformations:

### Case Modification

* **`.str.lower()`**: Converts all characters in the text vector to lowercase.
* **`.str.upper()`**: Converts all characters in the text vector to uppercase.
* **`.str.title()`**: Capitalizes the first character of every word in the string.
* **`.str.capitalize()`**: Capitalizes only the very first character of the entire string.
* **`.str.swapcase()`**: Inverts the case (transforms uppercase to lowercase and vice versa).

### Whitespace & Padding Cleansing

* **`.str.strip()`**: Removes leading and trailing spaces, tabs, and newline characters from both sides.
* **`.str.lstrip()`**: Trims whitespace characters exclusively from the left side of the string.
* **`.str.rstrip()`**: Trims whitespace characters exclusively from the right side of the string.
* **`.str.pad(width, side, fillchar)`**: Pads the string up to a specific minimum length with a custom character.
* **`.str.zfill(width)`**: Left-pads the numeric string with zeros up to the specified target length.

### Structural Splitting & Joining

* **`.str.split(pat)`**: Fragments the string into a Python list of substrings based on a delimiter character.
* **`.str.rsplit(pat, n)`**: Works like split, but scans from the right side of the string instead of the left.
* **`.str.cat(others, sep)`**: Concatenates or binds string columns together vertically or horizontally with a specified separator.
* **`.str.join(sep)`**: Merges elements of a list contained inside a cell into a unified string using a spacer character.

### Matching, Searching, and Filtering

* **`.str.contains(pat)`**: Evaluates if a substring or regular expression pattern exists anywhere inside the cell (returns `True`/`False`).
* **`.str.startswith(pat)`**: Checks if the string begins with a specific character sequence or prefix.
* **`.str.endswith(pat)`**: Checks if the string terminates with a specific character sequence or suffix.
* **`.str.find(sub)`**: Returns the lowest integer index position where a substring is discovered (returns `-1` if not found).
* **`.str.match(pat)`**: Determines if the string matches a regular expression starting strictly at the very first character.

### Substring Extraction & Editing

* **`.str.replace(old, new)`**: Substitutes specified character segments or regex patterns with an alternative string value.
* **`.str.slice(start, stop, step)`**: Extracts a precise slice of text based on character coordinate indexes.
* **`.str.get(i)`**: Directly extracts an element from a specific index position (highly effective for pulling items out of a split list).
* **`.str.len()`**: Calculates the absolute character count (length) of the string.
* **`.str.repeat(repeats)`**: Duplicates the string value a specified number of times down the column vector.

### String Structural Inspection (Type Checks)

* **`.str.isdigit()`**: Validates if all characters inside the text element are strictly numeric digits.
* **`.str.isalpha()`**: Validates if all characters inside the text element are strictly alphabetical letters.
* **`.str.isalnum()`**: Validates if the string contains only alphanumeric characters (no special symbols or spaces).
* **`.str.isspace()`**: Validates if the element contains exclusively whitespace characters.
