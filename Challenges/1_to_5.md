# Pandas Foundational Practice Challenges

This document contains five progressive data engineering challenges designed to test data frame creation, structural inspection, column manipulation, and row filtering.

---

## Challenge 1: The Sensor Log Alignment (Column-Oriented)

**Scenario:** A data ingestion pipeline receives an IoT device streaming payload as a dictionary of lists.

### Raw Data Input

```python
    import pandas as pd

    sensor_data = {
        'timestamp': ['2026-07-01 10:00', '2026-07-01 10:05', '2026-07-01 10:10'],
        'temperature': [22.5, 23.1, 22.8, 24.0],
        'status': ['OK', 'OK', 'WARN']
    }
```

### Your Tasks:
1. Run this code block. Identify the explicit Python error thrown and explain **why** it happens based on the structural design rules of a DataFrame.
2. Fix the underlying raw data structure manually so it compiles into a valid DataFrame named `df_sensor`.
3. Change the data type (`dtype`) of the `temperature` column from a float to an integer. Print `df_sensor.dtypes` to verify.

### My Solution:

```python
    import pandas as pd

    sensor_data = {
        'timestamp': ['2026-07-01 10:00', '2026-07-01 10:05', '2026-07-01 10:10'],
        'temperature': [22.5, 23.1, 22.8],  # Question 1 & 2: Removed 24.0
        'status': ['OK', 'OK', 'WARN']
    }

    df_sensor = pd.DataFrame(sensor_data)
    print(f"Before:\n{df_sensor.dtypes}\n")
    df_sensor['temperature'] = df_sensor['temperature'].astype(int)  # Question 3
    print(f"After:\n{df_sensor.dtypes}\n")  # Question 3
    print(f"Output:\n{df_sensor}")  # Check data of DF.
```

### My Output Verification:

    Before:
    timestamp       object
    temperature    float64
    status          object
    dtype: object

    After:
    timestamp      object
    temperature     int64
    status         object
    dtype: object

    Output:
            timestamp  temperature status
    0  2026-07-01 10:00           22     OK
    1  2026-07-01 10:05           23     OK
    2  2026-07-01 10:10           22   WARN
---

## Challenge 2: The E-Commerce Schema Edge-Case (Row-Oriented)

**Scenario:** An application database dumps an unstable JSON payload as a list of dictionaries. Keys are missing depending on whether a customer utilized a coupon code.

### Raw Data Input

```python
    import pandas as pd

    orders = [
        {'order_id': 5001, 'amount': 150.50, 'coupon': 'SAVE10'},
        {'order_id': 5002, 'amount': 99.00},
        {'order_id': 5003, 'amount': 210.00, 'coupon': 'WELCOME'}
    ]
```

### Your Tasks:

1. Load this raw list into a DataFrame named `df_orders`.
2. Inspect the structure using a single Pandas method that prints the total count of **Non-Null** values to analyze the `coupon` column.
3. Write a boolean mask to isolate and display only the rows where the `coupon` value is missing (`NaN`).

### My Solution:

```python
    import pandas as pd

    orders = [
        {'order_id': 5001, 'amount': 150.50, 'coupon': 'SAVE10'},
        {'order_id': 5002, 'amount': 99.00},
        {'order_id': 5003, 'amount': 210.00, 'coupon': 'WELCOME'}
    ]

    df_orders = pd.DataFrame(orders)    # Question 1
    print("Check Info of entire DF:\n")
    print(df_orders.info())
    print("\nCheck Info of Specific Series:\n")
    print(df_orders['coupon'].info())   # Question 2
    print("\n")
    print(df_orders[df_orders['coupon'].isnull()])  # Question 3
```

### My Output Verification:

    Check Info of entire DF:

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3 entries, 0 to 2
    Data columns (total 3 columns):
    #   Column    Non-Null Count  Dtype  
    ---  ------    --------------  -----  
    0   order_id  3 non-null      int64  
    1   amount    3 non-null      float64
    2   coupon    2 non-null      object 
    dtypes: float64(1), int64(1), object(1)
    memory usage: 204.0+ bytes
    None

    Check Info of Specific Series:

    <class 'pandas.core.series.Series'>
    RangeIndex: 3 entries, 0 to 2
    Series name: coupon
    Non-Null Count  Dtype 
    --------------  ----- 
    2 non-null      object
    dtypes: object(1)
    memory usage: 156.0+ bytes
    None


    order_id  amount coupon
    1      5002    99.0    NaN
---

## Challenge 3: Advanced Pipeline Sanitation (Complex Filtering)

**Scenario:** A master inventory table tracks items stored across different regional data centers. A highly specific subset of high-value targets must be extracted for an audit.

### Raw Data Input

```python
    import pandas as pd

    inventory = {
        'item_sku': ['SKU-01', 'SKU-02', 'SKU-03', 'SKU-04', 'SKU-05'],
        'warehouse': ['Mumbai', 'Delhi', 'Noida', 'Mumbai', 'Chennai'],
        'stock_count': [45, 12, 8, 150, 0],
        'unit_price': [1200, 4500, 850, 3100, 150]
    }
    df_inv = pd.DataFrame(inventory)
```

### Your Tasks:
1. Extract a multi-column subset containing *only* `item_sku` and `unit_price`. Assign it to a new variable.
2. Apply a complex multi-condition row filter to `df_inv` using the `&` (AND) operator to find items that match **both** of these criteria:
   * The `warehouse` must be either **Mumbai** or **Delhi** (utilizing the `.isin()` method).
   * The `unit_price` must be **greater than 1000**.
3. **Bonus Task:** Combine the row filter operation with bracket selection in a single execution line to display *only* the `item_sku` column for matching records.

### My Solution:

```python
    import pandas as pd

    inventory = {
        "item_sku": ["SKU-01", "SKU-02", "SKU-03", "SKU-04", "SKU-05"],
        "warehouse": ["Mumbai", "Delhi", "Noida", "Mumbai", "Chennai"],
        "stock_count": [45, 12, 8, 150, 0],
        "unit_price": [1200, 4500, 850, 3100, 150],
    }
    df_inv = pd.DataFrame(inventory)

    item_price = df_inv[["item_sku", "unit_price"]]  # Question 1
    print(f"{item_price}\n")

    print("Question 2:")
    print(
        df_inv[
            (df_inv["warehouse"].isin(["Mumbai", "Delhi"]))
            & (df_inv["unit_price"] > 1000)
        ]
    )  # Question 2

    print("\n")

    new_df = df_inv[
        (df_inv["warehouse"].isin(["Mumbai", "Delhi"]))
        & (df_inv["unit_price"] > 1000)
    ]  # Question 3
    print(f"Question 3:\n {new_df['item_sku']}")  # Question 3
```

### My Output Verification:

    item_sku  unit_price
    0   SKU-01        1200
    1   SKU-02        4500
    2   SKU-03         850
    3   SKU-04        3100
    4   SKU-05         150

    Question 2:
    item_sku warehouse  stock_count  unit_price
    0   SKU-01    Mumbai           45        1200
    1   SKU-02     Delhi           12        4500
    3   SKU-04    Mumbai          150        3100


    Question 3:
    0    SKU-01
    1    SKU-02
    3    SKU-04
    Name: item_sku, dtype: object
---

## Challenge 4: Derived Metric Calculations (Vector Math)

**Scenario:** A financial analytics job requires computing a downstream metric called **Total Inventory Valuation State**.

### Raw Data Input

```python
    import pandas as pd

    products = {
        'prod_id': [10, 20, 30],
        'quantity': [100, 250, 80],
        'cost_per_unit': [15.00, 8.50, 42.00]
    }
    df_prod = pd.DataFrame(products)
```

### Your Tasks:
1. Add a new computed column named `total_value` by multiplying the `quantity` vector by the `cost_per_unit` vector.
2. Add a second derived column named `tax_liability` representing a flat **18%** of the computed `total_value` column.
3. Print the final `df_prod` output table structure to verify both new columns exist.

### My Solution:

```python
    import pandas as pd

    products = {
        "prod_id": [10, 20, 30],
        "quantity": [100, 250, 80],
        "cost_per_unit": [15.00, 8.50, 42.00],
    }
    df_prod = pd.DataFrame(products)

    df_prod["total_value"] = (
        df_prod["quantity"] * df_prod["cost_per_unit"]
    )  # Question 1

    df_prod["tax_liability"] = df_prod["total_value"] * 18 / 100  # Question 2

    print(df_prod)  # Question 3
```
### My Output Verification:

    prod_id  quantity  cost_per_unit  total_value  tax_liability
    0       10       100           15.0       1500.0          270.0
    1       20       250            8.5       2125.0          382.5
    2       30        80           42.0       3360.0          604.8

---

## Challenge 5: Comprehensive Data Engine Simulation (The Final Challenge)

**Scenario:** An uncleaned transactional stream dump must be inspected, sanitized, partitioned, and enriched before analytical data warehouse loading.

### Raw Data Input

    import pandas as pd

    raw_stream = [
        {'tx_id': 9901, 'user': 'alpha', 'amount': '550', 'region': 'MUMBAI'},
        {'tx_id': 9902, 'user': 'beta', 'amount': '1200', 'region': 'DELHI'},
        {'tx_id': 9903, 'user': 'gamma', 'amount': '350', 'region': 'MUMBAI'},
        {'tx_id': 9904, 'user': 'delta', 'amount': '2100', 'region': 'NOIDA'},
        {'tx_id': 9905, 'user': 'epsilon', 'region': 'DELHI'} # Missing amount field
    ]

### Your Tasks:
1. Load the stream array into a DataFrame named `df_stream` and evaluate the mismatched types via `.dtypes`.
2. Clean the missing fields by assigning a temporary default value of `'0'` to any `NaN` values in the `amount` column.
3. Convert the entire `amount` column into a proper integer data type (`int64`) using `.astype()`.
4. Standardize the `region` column text from uppercase structures to lowercase structures.
5. Create a partitioned subset DataFrame called `df_high_value` containing only the `tx_id` and `user` columns where the sanitized `amount` is **strictly greater than 500**.

### My Solution:

```python
    import pandas as pd

    raw_stream = [
        {"tx_id": 9901, "user": "alpha", "amount": "550", "region": "MUMBAI"},
        {"tx_id": 9902, "user": "beta", "amount": "1200", "region": "DELHI"},
        {"tx_id": 9903, "user": "gamma", "amount": "350", "region": "MUMBAI"},
        {"tx_id": 9904, "user": "delta", "amount": "2100", "region": "NOIDA"},
        {
            "tx_id": 9905,
            "user": "epsilon",
            "region": "DELHI",
        },  # Missing amount field
    ]

    df_stream = pd.DataFrame(raw_stream)

    print(df_stream.dtypes)  # Question 1: amount needs to be int/float

    df_stream["amount"] = df_stream["amount"].fillna(0)  # Question 2

    df_stream["amount"] = df_stream["amount"].astype(int)  # Question 3

    df_stream["region"] = df_stream["region"].str.lower()  # Question 4

    df_high_value = df_stream[df_stream["amount"] > 500][
        ["tx_id", "user"]
    ]  # Question 5

    print(f"df_stream:\n{df_stream}\n")

    print(f"df_high_value:\n{df_high_value}")
```

### My Output Verification:

    tx_id      int64
    user      object
    amount    object
    region    object
    dtype: object
    df_stream:
    tx_id     user  amount  region
    0   9901    alpha     550  mumbai
    1   9902     beta    1200   delhi
    2   9903    gamma     350  mumbai
    3   9904    delta    2100   noida
    4   9905  epsilon       0   delhi

    df_high_value:
    tx_id   user
    0   9901  alpha
    1   9902   beta
    3   9904  delta
