# Exercise 2 - Partition Advisor

**Partition Advisor** generates intelligent recommendations for partitioning tables based on their size, usage patterns and data distribution.

In this exercise, we will use this **Partition Advisor** to generate partitioning recommendations for 5 different scnearios and apply those recommendations easily within **Recommendation App** from **HANA Cloud Central**.

## Exercise 2.1 Generate partitioning recommendations with Partition Advisor

For this hands-on practice, **five exemplifying scenarios** have been designed and set up in your instance.

You can find the table information for these five scenarios below.
- **T_SCENARIO_1**: Full unpartitioned table with primary key
- **T_SCENARIO_2**: Unpartitioned table with high data usage and complex query patterns
- **T_SCENARIO_3**: Unpartitioned time-based table with high access patterns
- **T_SCENARIO_4**: Range partitioned table with a full 'OTHERS' partition
- **T_SCENARIO_5**: Hash-hash partitioned table with some full partitions

The minimum rows for repartitioning, repartitioning threshold, and initial partition values were set to artificially low levels to facilitate demonstration scenarios with minimal data and reduced workload. Refer to the [table placement rule](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-administration-guide/table-placement-rules?locale=en-US) for detailed parameter guide.

After completing these steps you will have successfully generated five partitioning recommendations for your HANA Cloud instance from HANA Cloud Central.

1. **Open SAP BTP Cockpit** (Same with Exercise 1)
- Navigate to [SAP BTP Cockpit](https://tdct3ched1.accounts.ondemand.com/oauth2/authorize?response_type=code&scope=openid+email+profile&redirect_uri=https%3A%2F%2Femea.cockpit.btp.cloud.sap%2Flogin%2Fcallback&client_id=306ee77d-68d9-4398-ac62-1d07872563f9&state=EPkcyY---sTacmmvjflnPQ&code_challenge=fqM4tO2wlVaQLhRKfiqhS_2sXqA5WHfsG4QxvAc4oq4&code_challenge_method=S256).
- Login with your **_User Name_** and **_Password_**.


2. **Access HANA Cloud Central** (Same with Exercise 1)
- Click **Instances and Subscriptions** in the leftmost navigation panel.
- Click **SAP HANA Cloud** in the Application list to open HANA Cloud Central.
<br>![](/exercises/ex1/images/01_00.png)
- In HANA Cloud Central, you can see your HANA Cloud instance. Click the instance and open **Overview** page.
<br>![](/exercises/ex1/images/01_01.png)


3. **Switch On the Partition Advisor**
- Choose **Partition** from **Recommendations** App.
<br>![](/exercises/ex2/images/02_01.png)

- Click **Switch On**.
<br>![](/exercises/ex2/images/02_02.png)

- You need a dedicated **Partition Advisor User** to use the Partition Advisor. You can find more details about the Partition Advisor User in the [Getting Started](exercises/ex0/) section. The **Partition Advisor User** has been already created for this exercise. Add the following credential and click **Save**.

  - Username: _**PAUSER**_
  - Password: _**Partitionadvisor1**_
<br>![](/exercises/ex2/images/02_03.png)


4. **Generate Recommendations**
- The Partition Advisor is **On** and you can view the **Username**. Click **Generate Recommendations**.
<br>![](/exercises/ex2/images/02_04.png)

- Click **Add** to select target object(s) that you want the advisor to analyze against. 
<br>![](/exercises/ex2/images/02_05.png)

- You can choose **Object Type** between **Schema** or **Table**. Choose **Table** here and click **Schema** field to show a list of schema that the **Partition Advisor User** can view.
<br>![](/exercises/ex2/images/02_06.png)

- Select the schema, named **PA_DEMO**. Then you can view the 5 tables in the schema. Click **Add**.
<br>![](/exercises/ex2/images/02_07.png)
<br>![](/exercises/ex2/images/02_08.png)

- The target objects are added in the list. Click **Generate Recommendation**.
<br>![](/exercises/ex2/images/02_09.png)

- The advisor is generating recommendations for the selected objects.
<br>![](/exercises/ex2/images/02_10.png)

- Click **Refresh** icon on the top right side to see the progress updated.
<br>![](/exercises/ex2/images/02_11.png)

- The recommendations have been generated and the **Status** has changed to **Executed**. Go to **Recommendations** tab to view the available recommendations.
<br>![](/exercises/ex2/images/02_12.png)

- Here you can find the list of available recommendations for all 5 scenarios.
<br>![](/exercises/ex2/images/02_13.png)


## Exercise 2.2 Apply partitioning recommendations from Recommendation App

After completing these steps, you will have successfully applied all partitioning recommendations from the Recommendation App and validated that the partitioning actions have been implemented as suggested.

### Scenario 1: Full unpartitioned table with primary key

This scenario has set aggressive partitioning parameters for the table:
- MIN_ROWS_FOR PARTITIONING: 100
- REPARTITIONING_THRESHOLD: 100 (very low threshold)
- INITIAL_PARTITIONS: 3

1. **Review recommendation details for the table T_SCENARIO_1**
<br>![](/exercises/ex2/images/02_14a.png)
<br>![](/exercises/ex2/images/02_14b.png)
<br>![](/exercises/ex2/images/02_14c.png)
The advisor recommends **hash partitioning** on the primary key, **ID**, to resolve the full table issue.

2. **Check current partitioning status** 
- Before applying the recommendation, verify the current partitioning state of table 'T_SCENARIO_1'.

- Open the **SQL Console** for your instance.
<br>![](/exercises/ex2/images/02_15.png)

- Execute the following SQL script to check the current partitioning.
```SQL
SELECT TABLE_NAME, PARTITION_DEFINITION FROM PARTITIONED_TABLES WHERE TABLE_NAME='T_SCENARIO_1';
```
- The query returns no results, confirming that the table is currently unpartitioned.
<br>![](/exercises/ex2/images/02_16.png)

3. **Apply the recommendation**
- Click **Apply** in the recommendation detail page.
<br>![](/exercises/ex2/images/02_17.png)
- You can monitor the advisor applying the recommendation in the **Processes** tab.
<br>![](/exercises/ex2/images/02_18.png)

4. **Validate partitioning implementation**
- Re-run the same SQL script to confirm the partitioning has been applied.
<br>![](/exercises/ex2/images/02_19.png)
The table has been successfully hash partitioned into 12 partitions on the primary key to distribute data evenly across multiple partitions and improve query performance by avoiding full table scans.


### Scenario 2: Unpartitioned table with high data usage and complex query patterns

This scenario has simulated heavy query load by executing the same query 100 times, creating a usage pattern where a specific column is frequently queried.

1. **Review recommendation details for the table T_SCENARIO_2**
<br>![](/exercises/ex2/images/02_20.png)
The advisor identifies the 'EVENT_TYPE' column as the most frequently queried column and recommends hash partitioning on this column for optimal performance.

2. **Check current partitioning status** 
- Verify the current partitioning state using the same approach as Scenario 1.

- Run the following SQL script to check that the table is unpartitioned.
```SQL
SELECT TABLE_NAME, PARTITION_DEFINITION FROM PARTITIONED_TABLES WHERE TABLE_NAME='T_SCENARIO_2';
```

3. **Apply the recommendation**
- Click **Apply** in the recommendation detail page.
<br>![](/exercises/ex2/images/02_21.png)

4. **Validate partitioning implementation**
- Re-run the same SQL script to confirm the partitioning has been applied.
<br>![](/exercises/ex2/images/02_22.png)
The table has been successfully hash-partitioned into 3 partitions to improve query performance by distributing data based on the most-accessed column.

### Scenario 3: Unpartitioned time-based table with high access patterns

This scenario has simulated a time-based table with data spanning from 2019 to 2025, creating a historical dataset where recent "hot" data (2023 onwards) is frequently accessed, while older data (2019-2022) remains relatively unused.

1. **Review recommendation details for the table T_SCENARIO_3**
<br>![](/exercises/ex2/images/02_23.png)
The advisor suggests range partitioning to isolate the hot data (2023 onwards) in a single partition.

2. **Check current partitioning status** 
- Verify the current partitioning state using the same approach as Scenario 1.

- Run the following SQL script to check that the table is unpartitioned.
```SQL
SELECT TABLE_NAME, PARTITION_DEFINITION FROM PARTITIONED_TABLES WHERE TABLE_NAME='T_SCENARIO_3';
```

3. **Apply the recommendation**
- Click **Apply** in the recommendation detail page.

4. **Validate partitioning implementation**
- Re-run the same SQL script to confirm the partitioning has been applied.
<br>![](/exercises/ex2/images/02_24.png)
The table has been successfully range-partitioned into 3 partitions, isolating frequently accessed recent data in a dedicated partition and improving query performance by avoiding scans of older, unused data.


### Scenario 4: Range partitioned table with full 'OTHERS' partition

This scenario has created a range-partitioned table with a problematic 'OTHERS' partition.

1. **Review recommendation details for the table T_SCENARIO_4**
<br>![](/exercises/ex2/images/02_25.png)
The advisor recommends splitting the partition into more proper range-based partitions.

2. **Check current partitioning status** 
- To verify the current partitioning state of table 'T_SCENARIO_4', repeat the same steps from Scenario 1.

- Run the following SQL script to check the current partitioning state.
```SQL
SELECT TABLE_NAME, PARTITION_DEFINITION FROM PARTITIONED_TABLES WHERE TABLE_NAME='T_SCENARIO_4';
```
- The table is divided into partitions based on ranges of values in the 'O_ORDERKEY' column, but there are no explicit ranges defined, so all rows with any 'O_ORDERKEY' value are stored in the 'OTHERS' partition.
<br>![](/exercises/ex2/images/02_26.png)

- Check the performance by running the following query on a specific range of order keys before applying the recommendation.
```SQL
SELECT * FROM T_SCENARIO_4 WHERE O_ORDERKEY > 1000000 AND O_ORDERKEY < 1200000;
```

- Check **Execution Elapsed Time**, **Execution CPU Time** and **Server Peak Memory** in the **History** tab.
<br>![](/exercises/ex2/images/02_26_a.png)

3. **Apply the recommendation**
- Click **Apply** in the recommendation detail page.

- Check the applied recommendation by adding **Applied** in the **Status** filter.
<br>![](/exercises/ex2/images/02_27.png)

4. **Validate partitioning implementation**
- Re-run the same SQL script to confirm the partitioning has been applied. The oversized 'OTHERS' partition has been split into proper range-based partitions to improve query performance for range-based queries.
<br>![](/exercises/ex2/images/02_26_c.png)

- Check the performance with the same SQL script and confirm the performance improvement compared to before applying the recommendation.
```SQL
SELECT * FROM T_SCENARIO_4 WHERE O_ORDERKEY > 1000000 AND O_ORDERKEY < 1200000;
```

- You can see that the **Execution Elapsed Time**, **Execution CPU Time** and **Server Peak Memory** has been reduced significantly.
<br>![](/exercises/ex2/images/02_26_b.png)

### Scenario 5: Hash-hash partitioned table with some full partitions

This scenario has created an imbalanced multi-level hash partitioned table. The hash  partitioning on 'REGION' doesn't distribute data evenly, causing some partitions to become full while others remain relatively empty.

1. **Review recommendation details for the table T_SCENARIO_5**
<br>![](/exercises/ex2/images/02_28_a.png)
<br>![](/exercises/ex2/images/02_28_b.png)
The advisor proposes to increase the number of leaf partitions while keeping the partitioning hierarchy balanced.

2. **Check current partitioning status** 
- Check the current partitioning state of table 'T_SCENARIO_5' by repeating the same steps from Scenario 1.
```SQL
SELECT TABLE_NAME, PARTITION_DEFINITION FROM PARTITIONED_TABLES WHERE TABLE_NAME='T_SCENARIO_5';
```
- The table currently has a two-level partitioning scheme: the first level is hash partitioned with the 'REGION' value into 2 partitions, and the second level is hash subpartitioned with the 'CUSTOMER_ID' into 2 partitions.
<br>![](/exercises/ex2/images/02_29.png)

3. **Apply the recommendation**
- Click **Apply** in the recommendation detail page.

4. **Validate partitioning implementation**
- Re-run the same SQL script to confirm the partitioning has been applied.
<br>![](/exercises/ex2/images/02_30.png)
The table now has 16 leaf partitions to achieve better data distribution while maintaining partitioning hierarchy balance.

## Summary

You've successfully generated partitioning recommendations for your selected schema(s) and table(s) and applied them easily with a simple click of **Apply** from the **Recommendation App**. 

You can find more assets about Partition Advisor below.
- [Help Documentation](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-administration-guide/about-partition-advisor?locale=en-US)


Continue to - [Exercise 3 - Index Advisor](../ex3/README.md)
