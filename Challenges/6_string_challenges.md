# Pandas Vectorized String Manipulation Practice Challenges

This document contains six progressive data engineering challenges designed to test case conversions, text replacements, keyword filtering, data parsing via slicing, and list extraction using delimiters.

---

## Challenge 1: Standardizing Legacy System Identifiers (Case & Clean)

**Scenario:** A data lake dump from an older database records structural machine clusters using a mixture of messy casing and padding spaces. To align them with your clean reporting schemas, you must standardize their presentation.

### Raw Data Input

```python
    import pandas as pd

    server_dump = {
        'instance_id': [8001, 8002, 8003],
        'raw_cluster_name': ['  MUMBAI_prod_01  ', 'DELHI_Dev_02 ', '   noida_TEST_01  ']
    }
    df_servers = pd.DataFrame(server_dump)
```

### Your Tasks:

1. Strip all trailing and leading whitespaces from the `raw_cluster_name` column.
2. Force the entire text vector into strict **uppercase** lettering.
3. Save the finalized strings into a new column named `cluster_clean` and print the table.

### My Solution:

```python
    import pandas as pd

    server_dump = {
        "instance_id": [8001, 8002, 8003],
        "raw_cluster_name": [
            "  MUMBAI_prod_01  ",
            "DELHI_Dev_02 ",
            "   noida_TEST_01  ",
        ],
    }
    df_servers = pd.DataFrame(server_dump)

    df_servers["cluster_clean"] = (
        df_servers["raw_cluster_name"].str.strip().str.upper()
    )

    print("Our New DF:\n", df_servers)
```

### My Output Verification:

```
    Our New DF:
       instance_id    raw_cluster_name   cluster_clean
    0         8001    MUMBAI_prod_01    MUMBAI_PROD_01
    1         8002       DELHI_Dev_02     DELHI_DEV_02
    2         8003     noida_TEST_01     NOIDA_TEST_01
```

---

## Challenge 2: Masking Administrative Metadata (Substring Replacement)

**Scenario:** An application security log contains operational file paths. Before passing this dataset downstream to an analytics dashboard, you must replace sensitive folder pathways with generalized environment variables.

### Raw Data Input

```python
    import pandas as pd

    logs = {
        'log_id': [101, 102, 103],
        'file_path': ['/home/user/admin/config.json', '/home/user/admin/db_properties.xml', '/var/logs/app.log']
    }
    df_logs = pd.DataFrame(logs)
```

### Your Tasks:

1. Use a vectorized string method to search for the substring `'/home/user/admin/'` inside the `file_path` column.
2. Replace that explicit path segment with the generalized variable string `'`$CONF_DIR/'`'`.
3. Overwrite the `file_path` column with the updated configurations.

### My Solution:

```python
    import pandas as pd

    logs = {
        "log_id": [101, 102, 103],
        "file_path": [
            "/home/user/admin/config.json",
            "/home/user/admin/db_properties.xml",
            "/var/logs/app.log",
        ],
    }
    df_logs = pd.DataFrame(logs)

    find_string = df_logs["file_path"].str.find("/home/user/admin/")
    print("String starting pos:\n", find_string, "\n")

    df_logs["file_path"] = df_logs["file_path"].str.replace(
        "/home/user/admin/", "$CONF_DIR/"
    )

    print("Our updated DF:\n", df_logs)
```

### My Output Verification:

```
    String starting pos:
    0    0
    1    0
    2   -1
    Name: file_path, dtype: int64 

    Our updated DF:
       log_id                    file_path
    0     101        $CONF_DIR/config.json
    1     102  $CONF_DIR/db_properties.xml
    2     103            /var/logs/app.log
```

---

## Challenge 3: Flagging High-Risk Incidents (Text Searching)

**Scenario:** A cloud security monitoring gateway captures error strings from connected microservices. You must build an automated boolean row selection mask to extract rows containing specific alert keywords.

### Raw Data Input

```python
    import pandas as pd

    incidents = {
        'ticket_id': ['T-501', 'T-502', 'T-503', 'T-504'],
        'error_msg': ['Connection timed out safely', 'CRITICAL: Database auth failure', 'Minor page render delay', 'CRITICAL: Memory leak on node 5']
    }
    df_incidents = pd.DataFrame(incidents)
```

### Your Tasks:

1. Apply a vectorized string filtering method to evaluate if the text inside the `error_msg` column contains the phrase `'CRITICAL'`.
2. Build a row mask using that evaluation to isolate the critical tickets into a separate variable named `df_critical_alerts` and print it.

### My Solution:

```python
    import pandas as pd

    incidents = {
        "ticket_id": ["T-501", "T-502", "T-503", "T-504"],
        "error_msg": [
            "Connection timed out safely",
            "CRITICAL: Database auth failure",
            "Minor page render delay",
            "CRITICAL: Memory leak on node 5",
        ],
    }
    df_incidents = pd.DataFrame(incidents)

    critical_alerts = df_incidents["error_msg"].str.contains("CRITICAL")
    df_critical_alerts = df_incidents[critical_alerts]
    print(df_critical_alerts)
```

### My Output Verification:

```
      ticket_id                        error_msg
    1     T-502  CRITICAL: Database auth failure
    3     T-504  CRITICAL: Memory leak on node 5
```

---

## Challenge 4: Extracting Database Region Identifiers (String Slicing)

**Scenario:** A cluster tracking report stores composite database codes where the first four characters strictly represent the geographical region code. You need to parse this column without breaking the strings up into lists.

### Raw Data Input

```python
    import pandas as pd

    db_inventory = {
        'db_id': [9901, 9902, 9903],
        'composite_code': ['MUMB_PROD_REPLICA_01', 'DELH_DEV_MASTER_02', 'NOID_TEST_STAGE_01']
    }
    df_db = pd.DataFrame(db_inventory)
```

### Your Tasks:

1. Use positional character slicing via `.str[...]` to isolate the **first 4 characters** of each record inside the `composite_code` column.
2. Store these isolated 4-letter regional flags inside a new column named `geo_prefix`.

### My Solution:

```python
    import pandas as pd

    db_inventory = {
        "db_id": [9901, 9902, 9903],
        "composite_code": [
            "MUMB_PROD_REPLICA_01",
            "DELH_DEV_MASTER_02",
            "NOID_TEST_STAGE_01",
        ],
    }
    df_db = pd.DataFrame(db_inventory)

    df_db["geo_prefix"] = df_db["composite_code"].str[:4]
    print("Updated DF:\n", df_db)
```

### My Output Verification:

```
    Updated DF:
       db_id        composite_code geo_prefix
    0   9901  MUMB_PROD_REPLICA_01       MUMB
    1   9902    DELH_DEV_MASTER_02       DELH
    2   9903    NOID_TEST_STAGE_01       NOID
```

---

## Challenge 5: Parsing Semi-Structured Resource Tags (Text Splitting)

**Scenario:** A cloud billing report groups resource allocation metrics using an underscore-delimited text vector string format: `REGION_ENVIRONMENT_SPEC`. To build an OLAP data cube, you must isolate the middle environment component.

### Raw Data Input

```python
    import pandas as pd

    billing_tags = {
        'resource_id': ['RES-881', 'RES-882', 'RES-883'],
        'allocation_tag': ['MUM_PROD_16GB', 'DEL_DEV_8GB', 'NOID_TEST_32GB']
    }
    df_billing = pd.DataFrame(billing_tags)
```

### Your Tasks:

1. Split the `allocation_tag` column by its underscore (`_`) delimiter.
2. Use the structural `.str.get()` method to extract *only* the middle component (the environment state sitting at list index `1`).
3. Store this extracted element vector inside a new column named `env_tier`.

### My Solution:

```python
    import pandas as pd

    billing_tags = {
        "resource_id": ["RES-881", "RES-882", "RES-883"],
        "allocation_tag": ["MUM_PROD_16GB", "DEL_DEV_8GB", "NOID_TEST_32GB"],
    }
    df_billing = pd.DataFrame(billing_tags)

    df_billing["env_tier"] = df_billing["allocation_tag"].str.split("_").str.get(1)
    print(df_billing)
```

### My Output Verification:

```
      resource_id  allocation_tag env_tier
    0     RES-881   MUM_PROD_16GB     PROD
    1     RES-882     DEL_DEV_8GB      DEV
    2     RES-883  NOID_TEST_32GB     TEST
```

---

## Challenge 6: The User Registration Data Cleansing Pipeline (The Final Boss)

**Scenario:** An ingestion job captures raw user registrations via an external web form API. The incoming stream records unstructured user attributes, cluttered metadata markers, and legacy status flags. You must write a robust text-sanitization pipeline to parse the user details before loading them into a data warehouse target.

### Raw Data Input

```python
    import pandas as pd

    raw_users = {
        'account_id': [4401, 4402, 4403, 4404],
        'full_name_raw': ['  naveen kumar ', 'AMIT SHARMA  ', ' sara khan ', ' rajesh gupTA '],
        'meta_tag': ['USER_ROLE:DATA_ENG', 'USER_ROLE:DEVOPS', 'SYS_ROLE:ADMIN', 'USER_ROLE:DATA_ENG'],
        'legacy_status': ['STATUS_ACTIVE_VERIFIED', 'STATUS_PENDING_RETRY', 'STATUS_ACTIVE_HOLD', 'SYSTEM_ERR_REJECT']
    }
    df_raw = pd.DataFrame(raw_users)
```

### Your Tasks:

1. **Normalize Names:** Strip the whitespace padding from the `full_name_raw` column and apply title-case capitalization so names read cleanly (e.g., `'Naveen Kumar'`). Save this to `full_name_clean`.
2. **Isolate Account Roles:** Split the `meta_tag` column by its colon (`:`) separator and extract the actual role name component sitting at index `1`. Save this to a new column named `assigned_role`.
3. **Filter System Rejects:** Build a text mask using a string method to detect any rows where the `legacy_status` column contains the phrase `'SYSTEM_ERR'`. **Drop** these rejected rows from your pipeline subset entirely.
4. **Standardize Status Indicators:** On the remaining valid rows, use `.str.replace()` to strip out the redundant `'STATUS_'` prefix from the text elements in the `legacy_status` column, leaving only raw indicators like `'ACTIVE_VERIFIED'` or `'PENDING_RETRY'`.
5. Drop the original `full_name_raw` and `meta_tag` columns. Print the final cleaned master DataFrame grid.

### My Solution:

```python
    import pandas as pd

    raw_users = {
        "account_id": [4401, 4402, 4403, 4404],
        "full_name_raw": [
            "  naveen kumar ",
            "AMIT SHARMA  ",
            " sara khan ",
            " rajesh gupTA ",
        ],
        "meta_tag": [
            "USER_ROLE:DATA_ENG",
            "USER_ROLE:DEVOPS",
            "SYS_ROLE:ADMIN",
            "USER_ROLE:DATA_ENG",
        ],
        "legacy_status": [
            "STATUS_ACTIVE_VERIFIED",
            "STATUS_PENDING_RETRY",
            "STATUS_ACTIVE_HOLD",
            "SYSTEM_ERR_REJECT",
        ],
    }
    df_raw = pd.DataFrame(raw_users)

    # df_raw["full_name_clean"] = df_raw["full_name_raw"].str.strip().str.title()
    # df_raw["assigned_role"] = df_raw["meta_tag"].str.split(":").str.get(1)
    # mask = df_raw["legacy_status"].str.contains("SYSTEM_ERR")
    # df_new = df_raw[mask]
    # account_ids_list = df_new.index.tolist()
    # df_raw = df_raw.drop(index=account_ids_list)
    # df_raw["legacy_status"] = df_raw["legacy_status"].str.replace("STATUS_", "")
    # df_raw = df_raw.drop(columns=["full_name_raw", "meta_tag"])

    # print(df_raw)


    df_raw["full_name_clean"] = df_raw["full_name_raw"].str.strip().str.title()
    df_raw["assigned_role"] = df_raw["meta_tag"].str.split(":").str.get(1)

    # Create a mask that evaluates to True for rows you want to KEEP
    keep_mask = ~df_raw["legacy_status"].str.contains("SYSTEM_ERR")

    # Filter the DataFrame down directly in one step
    df_raw = df_raw[keep_mask]

    df_raw["legacy_status"] = df_raw["legacy_status"].str.replace("STATUS_", "")
    df_raw = df_raw.drop(columns=["full_name_raw", "meta_tag"])

    print(df_raw)
```

### My Output Verification:

```
       account_id    legacy_status full_name_clean assigned_role
    0        4401  ACTIVE_VERIFIED    Naveen Kumar      DATA_ENG
    1        4402    PENDING_RETRY     Amit Sharma        DEVOPS
    2        4403      ACTIVE_HOLD       Sara Khan         ADMIN
```

