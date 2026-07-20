# Advanced String Mining and Regular Expressions (Regex)

Raw unstructured staging data (such as application logs or raw API strings) often smashes metrics together into single columns. Data engineers use vectorized regular expressions via the `.str` namespace to mine, isolate, and replace targeted text values at high speeds.

---

## 1. Extracting Text Substrings (`.str.extract()`)

The `.str.extract()` method uses regex capture groups—defined by parentheses `()`—to search text strings and extract matching patterns directly into distinct columns.

### Core Token Reference

* **`([A-Z]+)`**: Capture group matching one or more consecutive uppercase letters.
* **`(\d+)`**: Capture group matching one or more consecutive digits.
* **`([a-zA-Z0-9_-]+)`**: Capture group matching standard alphanumeric strings, hyphens, or underscores.

**Example:**

```python
    import pandas as pd

    df = pd.DataFrame({'raw_log': ['ID:995_STATUS:OK', 'ID:102_STATUS:FAIL']})

    # Regex matches 'ID:', captures the digits, matches '_STATUS:', and captures letters
    pattern = r'ID:(\d+)_STATUS:([A-Z]+)'
    
    # Extract directly into a new tabular layout grid
    df[['user_id', 'status_flag']] = df['raw_log'].str.extract(pattern)
    print(df)
```

**Output:**

```
                  raw_log user_id status_flag
    0    ID:995_STATUS:OK     995          OK
    1  ID:102_STATUS:FAIL     102        FAIL
```

---

## 2. Advanced Text Replacements (`.str.replace()`)

While regular string replacements swap exact characters, regex-based `.str.replace()` allows you to match dynamic variable patterns and clear them out or re-format them instantly.

### Core Parameter Controls

* **`regex=True`**: Instructs the string engine to treat the matching pattern argument as a regular expression instead of literal text.

**Example:**

```python
    import pandas as pd

    df = pd.DataFrame({'billing_code': ['TX-8801 (USD)', 'TX-4402 (EUR)']})

    # Regex matches a space, an opening parenthesis, any characters, and a closing parenthesis
    pattern = r'\s\([A-Z]+\)'
    
    # Scrub the dynamic currency suffixes out completely
    df['clean_code'] = df['billing_code'].str.replace(pattern, '', regex=True)
    print(df)
```

**Output:**

```
        billing_code clean_code
    0  TX-8801 (USD)    TX-8801
    1  TX-4402 (EUR)    TX-4402
```
