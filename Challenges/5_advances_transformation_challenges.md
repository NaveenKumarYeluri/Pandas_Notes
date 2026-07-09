# Pandas Advanced Data Transformation Practice Challenges

This document contains six progressive data engineering challenges designed to test inline element-wise string formatting, horizontal row-wise evaluation across multi-column data grids, and strict categorical dictionary translation.

---

## Challenge 1: Log Metric Cleaning (Element-Wise `.apply()`)

**Scenario:** An application error log captures infrastructure node names with leading and trailing whitespaces along with irregular uppercase lettering. Before using these strings as keys in an aggregation step, they must be normalized.

### Raw Data Input

```python
    import pandas as pd

    raw_nodes = {
        'instance_id': [5001, 5002, 5003],
        'node_name': ['  MUMBAI-NOD-01 ', ' delhi-nod-02  ', '  NOIDA-NOD-03 ']
    }
    df_nodes = pd.DataFrame(raw_nodes)
```

### Your Tasks:

1. Use the `.apply()` method combined with an inline lambda function on the `node_name` column to strip the outer whitespaces and convert all characters to lowercase.
2. Save the cleaned values into a new column named `node_name_clean` and print the resulting table.

### My Solution:

```python
    import pandas as pd

    raw_nodes = {
        "instance_id": [5001, 5002, 5003],
        "node_name": ["  MUMBAI-NOD-01 ", " delhi-nod-02  ", "  NOIDA-NOD-03 "],
    }
    df_nodes = pd.DataFrame(raw_nodes)

    df_nodes["node_name_clean"] = df_nodes["node_name"].apply(
        lambda val: val.lower().strip()
    )  # Question 1, 2

    print("Output:\n", df_nodes)
```

### My Output Verification:

```
    Output:
       instance_id         node_name node_name_clean
    0         5001    MUMBAI-NOD-01    mumbai-nod-01
    1         5002    delhi-nod-02      delhi-nod-02
    2         5003     NOIDA-NOD-03     noida-nod-03
```

---

## Challenge 2: API Request Latency Categorization (Conditional `.apply()`)

**Scenario:** An API gateway tracks response latency metrics in milliseconds. A data engineer needs to map these continuous integers into concrete operational buckets to populate a real-time monitoring dashboard.

### Raw Data Input

```python
    import pandas as pd

    gateway_logs = {
        'request_id': ['REQ-A', 'REQ-B', 'REQ-C', 'REQ-D'],
        'latency_ms': [45, 1200, 250, 4100]
    }
    df_gateway = pd.DataFrame(gateway_logs)
```

### Your Tasks:

1. Write a custom inline conditional lambda function using `.apply()` on the `latency_ms` column to classify requests based on these thresholds:
   * If latency is strictly greater than 1000 ms, label it `'HIGH'`.
   * Otherwise, label it `'LOW'`.
2. Save the classifications into a column named `latency_tier`.

### My Solution:

```python
    import pandas as pd

    gateway_logs = {
        "request_id": ["REQ-A", "REQ-B", "REQ-C", "REQ-D"],
        "latency_ms": [45, 1200, 250, 4100],
    }
    df_gateway = pd.DataFrame(gateway_logs)

    df_gateway["latency_tier"] = df_gateway["latency_ms"].apply(
        lambda val: "HIGH" if val > 1000 else "LOW"
    )   # Question 1, 2

    print("Output:\n", df_gateway)
```

### My Output Verification:

```
    Output:
      request_id  latency_ms latency_tier
    0      REQ-A          45          LOW
    1      REQ-B        1200         HIGH
    2      REQ-C         250          LOW
    3      REQ-D        4100         HIGH
```

---

## Challenge 3: Environment Configuration Mapping (Dictionary `.map()`)

**Scenario:** A deployment inventory sheet uses short status characters to flag corporate server runtime environments. To prepare an engineering report, you must translate these codes into full, reader-friendly string designations.

### Raw Data Input

```python
    import pandas as pd

    inventory = {
        'server_handle': ['SRV-01', 'SRV-02', 'SRV-03', 'SRV-04'],
        'env_code': ['P', 'D', 'P', 'T']
    }
    df_inv = pd.DataFrame(inventory)
```

### Your Tasks:

1. Create a translation mapping dictionary where:
   * `'P'` maps to `'Production'`
   * `'D'` maps to `'Development'`
   * `'T'` maps to `'Testing'`
2. Use the `.map()` method to transform the `env_code` column into a new column named `environment_display`.

### My Solution:

```python
    import pandas as pd
    
    inventory = {
        "server_handle": ["SRV-01", "SRV-02", "SRV-03", "SRV-04"],
        "env_code": ["P", "D", "P", "T"],
    }
    df_inv = pd.DataFrame(inventory)
    
    env_dictionary = {
        "P": "Production",
        "D": "Development",
        "T": "Testing",
    }  # Question 1
    
    df_inv["environment_display"] = df_inv["env_code"].map(
        env_dictionary
    )  # Question 2
    
    print("Output:\n", df_inv)
```

### My Output Verification:

```
    Output:
      server_handle env_code environment_display
    0        SRV-01        P          Production
    1        SRV-02        D         Development
    2        SRV-03        P          Production
    3        SRV-04        T             Testing
```

---

## Challenge 4: Mapping Safety Validation (The Out-of-Bounds Trap)

**Scenario:** A billing table tracking regional cloud instances contains a legacy region code that does not match your current structural registry. You must run a dictionary mapping and evaluate how Pandas handles unmatched key records.

### Raw Data Input

```python
    import pandas as pd

    billing_stream = {
        'invoice_id': [7701, 7702, 7703],
        'region_flag': ['MUM', 'DEL', 'CHN']
    }
    df_bill = pd.DataFrame(billing_stream)

    region_registry = {
        'MUM': 'Mumbai',
        'DEL': 'Delhi'
    }
```

### Your Tasks:

1. Apply the `region_registry` map directly to the `region_flag` column and save the output to `region_name`.
2. Print the final DataFrame. Inspect the value generated for invoice `7703` (`CHN`). Explain what happens when a row value is missing from the dictionary mapping.

### My Solution:

```python
    import pandas as pd

    billing_stream = {
        "invoice_id": [7701, 7702, 7703],
        "region_flag": ["MUM", "DEL", "CHN"],
    }
    df_bill = pd.DataFrame(billing_stream)
    region_registry = {"MUM": "Mumbai", "DEL": "Delhi"}

    df_bill["region_name"] = df_bill["region_flag"].map(
        region_registry
    )  # Question 1
    print(
        "Output:\n",
        df_bill,
        "\n\nWe will have NaN incase of no match",
    )  # Question 2
```

### My Output Verification:

```
    Output:
       invoice_id region_flag region_name
    0        7701         MUM      Mumbai
    1        7702         DEL       Delhi
    2        7703         CHN         NaN 

    We will have NaN incase of no match
```

---

## Challenge 5: Multi-Cluster Risk Evaluation (Row-Wise `axis=1`)

**Scenario:** You are building an automated infrastructure alert engine. Instead of evaluating a single resource, the engine must evaluate both CPU usage and storage volumes across the same row to flag hardware assets facing stability risks.

### Raw Data Input

```python
    import pandas as pd

    cluster_telemetry = {
        'cluster_id': ['CL-A', 'CL-B', 'CL-C', 'CL-D'],
        'cpu_utilization_pct': [45.2, 91.4, 33.1, 88.5],
        'disk_utilization_pct': [55.0, 42.1, 89.6, 92.0]
    }
    df_cluster = pd.DataFrame(cluster_telemetry)
```

### Your Tasks:

1. Use the `.apply()` method with **`axis=1`** to run a row-wise evaluation down the DataFrame grid.
2. Write a lambda expression that flags a cluster as `'CRITICAL'` if **either** `cpu_utilization_pct > 90` **or** `disk_utilization_pct > 85`. Otherwise, label it `'HEALTHY'`. Save this inside a column named `cluster_risk`.

### My Solution:

```python
    import pandas as pd
    
    cluster_telemetry = {
        "cluster_id": ["CL-A", "CL-B", "CL-C", "CL-D"],
        "cpu_utilization_pct": [45.2, 91.4, 33.1, 88.5],
        "disk_utilization_pct": [55.0, 42.1, 89.6, 92.0],
    }
    df_cluster = pd.DataFrame(cluster_telemetry)
    
    df_cluster["cluster_risk"] = df_cluster.apply(
        lambda row: (
            "CRITICAL"
            if row["cpu_utilization_pct"] > 90 or row["disk_utilization_pct"] > 85
            else "HEALTHY"
        ),
        axis=1,
    )
    
    print("Our DF:\n", df_cluster)
```

### My Output Verification:

```
    Our DF:
      cluster_id  cpu_utilization_pct  disk_utilization_pct cluster_risk
    0       CL-A                 45.2                  55.0      HEALTHY
    1       CL-B                 91.4                  42.1     CRITICAL
    2       CL-C                 33.1                  89.6     CRITICAL
    3       CL-D                 88.5                  92.0     CRITICAL
```

---

## Challenge 6: The Streaming Log Sanitization Pipeline (The Final Boss)

**Scenario:** An uncleaned ingestion stream records raw server events. Before moving this stream downstream to an analytical storage data warehouse, you must pass it through a multi-stage transformation pipeline to clean metadata fields, calculate row-level financial classifications, and map operational states.

### Raw Data Input

```python
    import pandas as pd

    raw_stream = {
        'event_id': [1101, 1102, 1103, 1104],
        'client_email': [' admin@AWS.com ', ' USER12@saas.com', 'DevTeam@Aws.com ', ' billing@saas.com'],
        'execution_cost': [15.20, 145.00, 8.50, 210.50],
        'state_flag': ['S', 'F', 'S', 'S']
    }
    df_stream = pd.DataFrame(raw_stream)

    state_dictionary = {
        'S': 'Success',
        'F': 'Failed'
    }
```

### Your Tasks:

1. **Clean Contact Metadata:** Standardize the `client_email` column by removing trailing/leading whitespaces and casting all characters to lowercase.
2. **Translate Core State:** Convert the `state_flag` codes into readable descriptions (`'Success'` or `'Failed'`) using the provided `state_dictionary` via `.map()`. Save it as `state_desc` and drop the original flag column.
3. **Compute Row-Level Operational Tiers:** Apply a horizontal row-wise custom evaluation (`axis=1`) to determine an infrastructure billing class based on the cleaned email origin and price points:
   * If the standardized email ends with `'aws.com'` **and** the `execution_cost` is strictly greater than `10.00`, classify the row as `'PREMIUM_CLOUD'`.
   * Otherwise, classify the row as `'STANDARD_SAAS'`.
4. Save this classification inside a column named `billing_tier`. Print the final master DataFrame grid.

### My Solution:

```python
    import pandas as pd

    raw_stream = {
        "event_id": [1101, 1102, 1103, 1104],
        "client_email": [
            " admin@AWS.com ",
            " USER12@saas.com",
            "DevTeam@Aws.com ",
            " billing@saas.com",
        ],
        "execution_cost": [15.20, 145.00, 8.50, 210.50],
        "state_flag": ["S", "F", "S", "S"],
    }
    df_stream = pd.DataFrame(raw_stream)

    state_dictionary = {"S": "Success", "F": "Failed"}

    df_stream["client_email"] = (
        df_stream["client_email"].str.lower().str.strip()
    )  # Question 1

    df_stream["state_desc"] = df_stream["state_flag"].map(
        state_dictionary
    )  # Question 2
    df_stream = df_stream.drop(columns="state_flag")  # Question 2

    df_stream["billing_tier"] = df_stream.apply(
        lambda row: (
            "PREMIUM_CLOUD"
            if row["execution_cost"] > 10
            and row["client_email"].endswith("aws.com")
            else "STANDARD_SAAS"
        ),
        axis=1,
    )  # Question 3

    print("Out Final DF:\n", df_stream)  # Question 4
```

### My Output Verification:

```
    Out Final DF:
       event_id      client_email  execution_cost state_desc   billing_tier
    0      1101     admin@aws.com            15.2    Success  PREMIUM_CLOUD
    1      1102   user12@saas.com           145.0     Failed  STANDARD_SAAS
    2      1103   devteam@aws.com             8.5    Success  STANDARD_SAAS
    3      1104  billing@saas.com           210.5    Success  STANDARD_SAAS
```

