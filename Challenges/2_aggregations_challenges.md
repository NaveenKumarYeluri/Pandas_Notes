# Pandas Aggregation & Summary Statistics Practice Challenges

This document contains four progressive data engineering challenges designed to test descriptive summary statistics, categorical distributions, and segmented data groupings.

---

## Challenge 1: The Cloud Fleet Resource Audit (Summary Statistics)

**Scenario:** A cloud infrastructure log tracks the daily CPU utilization metrics of server nodes running inside a specific cluster. A data engineer needs to establish an operational baseline for resource efficiency.

### Raw Data Input

```python
    import pandas as pd

    fleet_logs = {
        'node_id': [1001, 1002, 1003, 1004, 1005],
        'avg_cpu_pct': [45.2, 88.7, 12.1, 91.4, 34.8],
        'ram_gb': [16, 32, 16, 64, 32]
    }
    df_fleet = pd.DataFrame(fleet_logs)
```

### Your Tasks:
1. Extract and isolate the absolute maximum CPU utilization value found in `avg_cpu_pct`.
2. Compute the arithmetic mean (average) of the `avg_cpu_pct` vector.
3. Execute a single bulk inspection method on `df_fleet` that instantly displays count, mean, standard deviation, and percentiles for all numeric columns.

### My Solution:

```python
    import pandas as pd

    fleet_logs = {
        "node_id": [1001, 1002, 1003, 1004, 1005],
        "avg_cpu_pct": [45.2, 88.7, 12.1, 91.4, 34.8],
        "ram_gb": [16, 32, 16, 64, 32],
    }
    df_fleet = pd.DataFrame(fleet_logs)

    max_cpu_utilization = df_fleet["avg_cpu_pct"].max()
    print("Max CPU utilization: ", max_cpu_utilization, "\n")  # Question 1

    mean_cpu = df_fleet["avg_cpu_pct"].mean()
    print("Arithmetic Mean of avg_cpu_pct: ", mean_cpu, "\n")  # Question 2

    print("Bulk operation on df_fleet:\n", df_fleet.describe())  # Question 3
```

### My Output Verification:

```
    Max CPU utilization:  91.4 

    Arithmetic Mean of avg_cpu_pct:  54.44 

    Bulk operation on df_fleet
                node_id  avg_cpu_pct     ram_gb
    count     5.000000     5.000000   5.000000
    mean   1003.000000    54.440000  32.000000
    std       1.581139    34.653903  19.595918
    min    1001.000000    12.100000  16.000000
    25%    1002.000000    34.800000  16.000000
    50%    1003.000000    45.200000  32.000000
    75%    1004.000000    88.700000  32.000000
    max    1005.000000    91.400000  64.000000
```

---

## Challenge 2: API Gateway Error Distribution (Frequency Counting)

**Scenario:** An API gateway stream captures inbound response states. Before parsing the payload data, you must evaluate the structural frequency distribution of HTTP response codes to isolate failing routes.

### Raw Data Input

```python
    import pandas as pd

    gateway_stream = {
        'request_id': ['REQ-01', 'REQ-02', 'REQ-03', 'REQ-04', 'REQ-05', 'REQ-06'],
        'status_code': ['200 OK', '500 Internal Error', '200 OK', '404 Not Found', '200 OK', '500 Internal Error']
    }
    df_gateway = pd.DataFrame(gateway_stream)
```

### Your Tasks:

1. Use a single Pandas Series method to calculate the absolute frequency count of each unique string variant inside the `status_code` column.
2. Review the resulting series structure. What column or structural element from the original `df_gateway` became the **Index** of this new output series?

### My Solution:

```python
    import pandas as pd

    gateway_stream = {
        "request_id": ["REQ-01", "REQ-02", "REQ-03", "REQ-04", "REQ-05", "REQ-06"],
        "status_code": [
            "200 OK",
            "500 Internal Error",
            "200 OK",
            "404 Not Found",
            "200 OK",
            "500 Internal Error",
        ],
    }
    df_gateway = pd.DataFrame(gateway_stream)

    status_codes_frequency = df_gateway["status_code"].value_counts()
    print("", status_codes_frequency, "\n")  # Question 1
    
    print(
        "Index is based on counts of each status_code, follows descending order",
        "i.e., highest to lowest",
    )  # Question 2
```

### My Output Verification:

```
    status_code
    200 OK                3
    500 Internal Error    2
    404 Not Found         1
    Name: count, dtype: int64
    
    Index is based on counts of each status_code, follows descending order i.e., highest to lowest
```

---

## Challenge 3: Regional Sales Ingestion Pipeline (Basic GroupBy)

**Scenario:** A retail transaction system dumps an unaggregated transactional stream. You need to group this information to determine which region generates the highest cumulative order valuation.

### Raw Data Input

```python
    import pandas as pd

    transactions = {
        'tx_id': [55001, 55002, 55003, 55004, 55005],
        'region': ['Mumbai', 'Delhi', 'Mumbai', 'Noida', 'Delhi'],
        'order_value': [12000, 45000, 8500, 31000, 15000]
    }
    df_tx = pd.DataFrame(transactions)
```

### Your Tasks:

1. Apply the split-apply-combine method (`.groupby()`) to segment rows by `region` and compute the total `sum` of `order_value` inside each regional partition.
2. In a single line of code, execute a groupby operation that groups by `region` but extracts the **maximum single transaction** value (`.max()`) for each region.

### My Solution:

```python
    import pandas as pd

    transactions = {
        "tx_id": [55001, 55002, 55003, 55004, 55005],
        "region": ["Mumbai", "Delhi", "Mumbai", "Noida", "Delhi"],
        "order_value": [12000, 45000, 8500, 31000, 15000],
    }
    df_tx = pd.DataFrame(transactions)

    sum_of_order_value = df_tx.groupby("region")["order_value"].sum()
    print(
        "Sum of Orders Value by Region:\n", sum_of_order_value, "\n"
    )  # Question 1

    print(
        "Max order value per Region:\n",
        df_tx.groupby("region")["order_value"].max(),
    )  # Question 2
```

### My Output Verification:

```
    Sum of Orders Value by Region:
    region
    Delhi     60000
    Mumbai    20500
    Noida     31000
    Name: order_value, dtype: int64 

    Max order value per Region:
    region
    Delhi     45000
    Mumbai    12000
    Noida     31000
    Name: order_value, dtype: int64
```

---

## Challenge 4: E-Commerce Product Category Performance (Multi-Metric Aggregation)

**Scenario:** A data warehouse job requires transforming catalog inventories into a matrix that profiles every product category based on total stock availability, average price points, and active item variations.

### Raw Data Input

```python
    import pandas as pd

    catalog = {
        'product_name': ['Laptop', 'Mouse', 'Keyboard', 'Smartphone', 'Monitor', 'Headphones'],
        'category': ['Electronics', 'Accessories', 'Accessories', 'Electronics', 'Electronics', 'Accessories'],
        'stock_count': [15, 120, 85, 40, 22, 65],
        'unit_price': [75000, 1200, 3500, 45000, 18000, 2500]
    }
    df_catalog = pd.DataFrame(catalog)
```

### Your Tasks:

1. Group the catalog DataFrame by the `category` column.
2. Using the multi-metric aggregate function (`.agg()`), process the grouped object to calculate three distinct metrics simultaneously:
   * The **sum** of all values in the `stock_count` column.
   * The **mean** (average) of the values in the `unit_price` column.
   * The **count** of items to determine the total product variants per category.

### My Solution:

```python
    import pandas as pd

    catalog = {
        "product_name": [
            "Laptop",
            "Mouse",
            "Keyboard",
            "Smartphone",
            "Monitor",
            "Headphones",
        ],
        "category": [
            "Electronics",
            "Accessories",
            "Accessories",
            "Electronics",
            "Electronics",
            "Accessories",
        ],
        "stock_count": [15, 120, 85, 40, 22, 65],
        "unit_price": [75000, 1200, 3500, 45000, 18000, 2500],
    }
    df_catalog = pd.DataFrame(catalog)

    metrics = df_catalog.groupby("category").agg(
        {"stock_count": "sum", "unit_price": "mean", "product_name": "count"}
    )

    print("metrics:", metrics)

    """
    metrics = df_catalog.groupby("category").agg(
        sum_stock_count=("stock_count", "sum"),
        mean_unit_price=("unit_price", "mean"),
        count_items_per_category=("product_name", "count"),
    )

    print("metrics:", metrics)
    """
```

### My Output Verification:

```
    metrics:              stock_count  unit_price  product_name
    category                                          
    Accessories          270      2400.0             3
    Electronics           77     46000.0             3
```

---

## Challenge 5: Incomplete Financial Stream Audit (The Data Cleansing Trap)

**Scenario:** A nightly transactional payload arrives with missing numeric measurements due to network dropouts. You must isolate categorical distributions, clean empty data points, and aggregate financial metrics without letting missing values (`NaN`) skew the averages.

### Raw Data Input

```python
    import pandas as pd
    import numpy as np

    dirty_stream = {
        'tx_id': [8801, 8802, 8803, 8804, 8805, 8806],
        'merchant': ['Cloud_AWS', 'SaaS_Tool', 'Cloud_AWS', 'SaaS_Tool', 'Cloud_AWS', 'Hardware_Vendor'],
        'billing_amount': [15000, np.nan, 22000, 4500, np.nan, 85000]
    }
    df_dirty = pd.DataFrame(dirty_stream)
```

### Your Tasks:

1. Run a categorical frequency count on the `merchant` column to check transaction volumes.
2. Clean the dataset: Overwrite all missing `NaN` values in the `billing_amount` column with a temporary fallback value of `0` using `.fillna()`.
3. Group the sanitized DataFrame by `merchant` and calculate the **mean** and **sum** of the `billing_amount` column simultaneously.

### My Solution:

```python
    import pandas as pd

    dirty_stream = {
        "tx_id": [8801, 8802, 8803, 8804, 8805, 8806],
        "merchant": [
            "Cloud_AWS",
            "SaaS_Tool",
            "Cloud_AWS",
            "SaaS_Tool",
            "Cloud_AWS",
            "Hardware_Vendor",
        ],
        "billing_amount": [15000, np.nan, 22000, 4500, np.nan, 85000],
    }
    df_dirty = pd.DataFrame(dirty_stream)

    categories = df_dirty["merchant"].value_counts()
    print("Categories:", categories, "\n")  # Question 1

    df_dirty["billing_amount"] = df_dirty["billing_amount"].fillna(0)
    print(df_dirty)  # Question 2

    metrics = df_dirty.groupby("merchant").agg(
        billing_amount_mean=("billing_amount", "mean"),
        billing_amount_sum=("billing_amount", "sum"),
    )
    print("\nmetrics:", metrics)  # Question 3
```

### My Output Verification:

```
    Categories: merchant
    Cloud_AWS          3
    SaaS_Tool          2
    Hardware_Vendor    1
    Name: count, dtype: int64 

    tx_id         merchant  billing_amount
    0   8801        Cloud_AWS         15000.0
    1   8802        SaaS_Tool             0.0
    2   8803        Cloud_AWS         22000.0
    3   8804        SaaS_Tool          4500.0
    4   8805        Cloud_AWS             0.0
    5   8806  Hardware_Vendor         85000.0

    metrics:                  billing_amount_mean  billing_amount_sum
    merchant                                                
    Cloud_AWS               12333.333333             37000.0
    Hardware_Vendor         85000.000000             85000.0
    SaaS_Tool                2250.000000              4500.0
```

---

## Challenge 6: Multi-Cluster Resource Efficiency Index (Advanced Index Reset)

**Scenario:** You are building an infrastructure dashboard matrix. You need to group tracking rows across multiple system parameters and calculate a custom derived metric called the **RAM-to-CPU Operational Ratio**.

### Raw Data Input

```python
    import pandas as pd

    cluster_metrics = {
        'datacenter': ['Mumbai', 'Delhi', 'Mumbai', 'Delhi', 'Noida', 'Mumbai'],
        'environment': ['Prod', 'Prod', 'Dev', 'Test', 'Prod', 'Prod'],
        'allocated_cpu': [64, 128, 16, 32, 64, 128],
        'allocated_ram_gb': [256, 512, 64, 128, 256, 512]
    }
    df_cluster = pd.DataFrame(cluster_metrics)
```

### Your Tasks:

1. Group the DataFrame by the `datacenter` column and calculate the total cumulative sum (`.sum()`) for both `allocated_cpu` and `allocated_ram_gb`. Assign this result to a new variable called `df_summary`.
2. Look closely at `df_summary`. Notice that `datacenter` has become the row Index. To turn it back into a standard data column, apply the `.reset_index()` method directly to `df_summary`. 
3. On this flattened summary DataFrame, add a brand-new computed column named `ram_per_cpu_ratio` by dividing the total `allocated_ram_gb` vector by the total `allocated_cpu` vector.

### My Solution:

```python
    import pandas as pd

    cluster_metrics = {
        "datacenter": ["Mumbai", "Delhi", "Mumbai", "Delhi", "Noida", "Mumbai"],
        "environment": ["Prod", "Prod", "Dev", "Test", "Prod", "Prod"],
        "allocated_cpu": [64, 128, 16, 32, 64, 128],
        "allocated_ram_gb": [256, 512, 64, 128, 256, 512],
    }
    df_cluster = pd.DataFrame(cluster_metrics)

    df_summary = df_cluster.groupby("datacenter").agg(
        allocated_cpu_sum=("allocated_cpu", "sum"),
        allocated_ram_gb_sum=("allocated_ram_gb", "sum"),
    )
    print("df summary Question 1:\n", df_summary, "\n")  # Question 1
    print("df summary Question 2:\n", df_summary.reset_index(), "\n")  # Question 2

    """
    df_summary = (
        df_cluster.groupby("datacenter")
        .agg(
            allocated_cpu_sum=("allocated_cpu", "sum"),
            allocated_ram_gb_sum=("allocated_ram_gb", "sum"),
        )
        .reset_index()
    )
    print("df summary: ", df_summary)
    """  # Question 2

    df_flattened = df_summary.reset_index()
    df_flattened["ram_per_cpu_ratio"] = (
        df_cluster["allocated_ram_gb"] / df_cluster["allocated_cpu"]
    )
    print("flattened DF Question 3:\n", df_flattened)  # Question 3
```

### My Output Verification:

```
    df summary Question 1:
                allocated_cpu_sum  allocated_ram_gb_sum
    datacenter                                         
    Delhi                     160                   640
    Mumbai                    208                   832
    Noida                      64                   256 

    df summary Question 2:
    datacenter  allocated_cpu_sum  allocated_ram_gb_sum
    0      Delhi                160                   640
    1     Mumbai                208                   832
    2      Noida                 64                   256 

    flattened DF Question 3:
    datacenter  allocated_cpu_sum  allocated_ram_gb_sum  ram_per_cpu_ratio
    0      Delhi                160                   640                4.0
    1     Mumbai                208                   832                4.0
    2      Noida                 64                   256                4.0
```
