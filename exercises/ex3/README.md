# Exercise 3 - Index Advisor

In this exercise, you will create a dataset and generate index recommendations using **Recommendation App** in **HANA Cloud Central**. You will then manually apply these recommendations through the **SQL Console** in **HANA Cloud Central** and validate their performance benefits. 

## What is Index Advisor?

**Index Advisor** is a rule-based advisor that recommends creating or dropping indexes based on statistics such as scan counts and index lookup counts. It also considers additional metadata to identify the most suitable candidates for index recommendations.

The current version of the advisor supports queries involving <ins>single tables only</ins>. Future enhancements will extend its capabilities to handle more complex queries and additional features.

## Dataset Information and Preparation

For this hands-on exercise, two sample tables will be created and used. These are **artifically designed** to serve as candidates for index recommendations.

- **MOVIES**: A table with three columns (*ID*, *GENRE*, *STATUS*) and **60 million records**
- **MOVIES2**: A table with three columns (*ID*, *GENRE*, *STATUS*) and **100 records**

Open the **SQL Console** from HANA Cloud Central and connect using the **DBADMIN** user. Execute the SQL script provided below to generate a large, skewed dataset and create a query workload that is intentionally resource-intensive.

**SQL Script** (Estimated execution time: approx. 3 min)
```SQL
-- Expensive Statement Collection Setting
ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('expensive_statement', 'enable') = 'true' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('expensive_statement', 'threshold_duration') = '1000000' WITH RECONFIGURE;

-- Step 1: Create the tables needed by the procedure.
CREATE TABLE GENRE_LIST (ID INT PRIMARY KEY, ITEM VARCHAR(100));
INSERT INTO GENRE_LIST VALUES (0,'Action');
INSERT INTO GENRE_LIST VALUES (1,'Comedy');
INSERT INTO GENRE_LIST VALUES (2,'Drama');
INSERT INTO GENRE_LIST VALUES (3,'Sci-Fi');
INSERT INTO GENRE_LIST VALUES (4,'Thriller');
INSERT INTO GENRE_LIST VALUES (5,'Romance');
INSERT INTO GENRE_LIST VALUES (6,'Animation');
INSERT INTO GENRE_LIST VALUES (7,'Horror');
INSERT INTO GENRE_LIST VALUES (8,'Documentary');
INSERT INTO GENRE_LIST VALUES (9,'Fantasy');

CREATE TABLE STATUS_LIST (ID INT PRIMARY KEY, ITEM VARCHAR(100));
INSERT INTO STATUS_LIST VALUES (0, 'Coming Soon');
INSERT INTO STATUS_LIST VALUES (1, 'Premiere Screening');
INSERT INTO STATUS_LIST VALUES (2, 'Rating Pending');
INSERT INTO STATUS_LIST VALUES (3, 'Trailer Released');
INSERT INTO STATUS_LIST VALUES (4, 'Poster Released');
INSERT INTO STATUS_LIST VALUES (5, 'Now Playing');
INSERT INTO STATUS_LIST VALUES (6, 'IMAX Screening');
INSERT INTO STATUS_LIST VALUES (7, '4DX Screening');
INSERT INTO STATUS_LIST VALUES (8, 'Early Bird Discount');
INSERT INTO STATUS_LIST VALUES (9, 'Late Night Show');
INSERT INTO STATUS_LIST VALUES (10, 'Stage Greeting');
INSERT INTO STATUS_LIST VALUES (11, 'Guest Visit (GV)');
INSERT INTO STATUS_LIST VALUES (12, 'Merchandise Event');
INSERT INTO STATUS_LIST VALUES (13, '1+1 Ticket Event');
INSERT INTO STATUS_LIST VALUES (14, 'Re-release');
INSERT INTO STATUS_LIST VALUES (15, 'Screening Ended');
INSERT INTO STATUS_LIST VALUES (16, 'VOD Available');
INSERT INTO STATUS_LIST VALUES (17, 'OTT Release');
INSERT INTO STATUS_LIST VALUES (18, 'Director''s Cut');
INSERT INTO STATUS_LIST VALUES (19, 'Film Festival Entry');

-- Create a MOVIES table with three columns
CREATE TABLE MOVIES (ID INTEGER, GENRE VARCHAR(100), STATUS VARCHAR(100));

-- Insert the data in MOVIES table
INSERT INTO MOVIES (ID, GENRE, STATUS)
SELECT
    -- 1. Generate 60M sequential IDs.
    T.GENERATED_PERIOD_START AS ID,

    -- 2. Generate Genre with skewed distribution.
    CASE
        WHEN MOD(T.GENERATED_PERIOD_START, 10000000) = 0 THEN 'Action' -- Every 10M row gets 'Action'.
        ELSE GL.ITEM
    END AS GENRE,

    -- 3. Generate Status with skewed distribution.
    CASE
        WHEN MOD(T.GENERATED_PERIOD_START, 10000000) = 0 THEN 'Coming Soon' -- Every 10M row gets 'Coming Soon'.
        ELSE SL.ITEM
    END AS STATUS

FROM
    -- Generate a virtual table with 60M numbers using the HANA series generator.
    SERIES_GENERATE_INTEGER(1, 1, 60000001) AS T
    -- JOIN with the remaining values for the round-robin logic.
    LEFT JOIN GENRE_LIST AS GL ON GL.ID = MOD(T.GENERATED_PERIOD_START - 1, 9) + 1
    LEFT JOIN STATUS_LIST AS SL ON SL.ID = MOD(T.GENERATED_PERIOD_START - 1, 19) + 1;


-- Create a MOVIES2 table with three columns
CREATE TABLE MOVIES2 (ID INTEGER, GENRE VARCHAR(100), STATUS VARCHAR(100));

-- Insert only 100 rows with repeated patterns
INSERT INTO MOVIES2 (ID, GENRE, STATUS)
SELECT
    ROW_NUMBER() OVER() AS ID,
    CASE MOD(ROW_NUMBER() OVER(), 4)
        WHEN 0 THEN 'Action'
        WHEN 1 THEN 'Comedy' 
        WHEN 2 THEN 'Drama'
        ELSE 'Thriller'
    END AS GENRE,
    CASE MOD(ROW_NUMBER() OVER(), 3)
        WHEN 0 THEN 'Coming Soon'
        WHEN 1 THEN 'Now Playing'
        ELSE 'Rating Pending'
    END AS STATUS
FROM SERIES_GENERATE_INTEGER(1, 1, 101);

-- Create indexes that will not be used
CREATE INDEX MOVIES2_GENRE ON MOVIES2(GENRE);
CREATE INDEX MOVIES2_STATUS ON MOVIES2(STATUS);

-- Delta Merge
MERGE DELTA OF MOVIES;
 
-- Off Auto Merge
ALTER TABLE MOVIES DISABLE AUTOMERGE;
ALTER TABLE MOVIES2 DISABLE AUTOMERGE;

-- Plan Cache Clear
ALTER SYSTEM CLEAR SQL PLAN CACHE;

-- A procedure that generates a workload by repeatedly executing two SELECT queries for a specified number of iterations.
CREATE OR REPLACE PROCEDURE GENERATE_SELECT_WORKLOAD(IN ITERATION_COUNT INT)
LANGUAGE SQLSCRIPT AS
BEGIN
    DECLARE dummy_count INT;
    DECLARE i INT;

    FOR i IN 1..ITERATION_COUNT DO
        -- 1. Execute query for GENRE = 'Action'
        SELECT COUNT(GENRE) INTO dummy_count FROM MOVIES WHERE GENRE = 'Action';

        -- 2. Execute query for STATUS = 'Coming Soon'
        SELECT COUNT(STATUS) INTO dummy_count FROM MOVIES WHERE STATUS = 'Coming Soon';
    END FOR;
END;

-- Call the workload generation procedure (repeating each query 10,000 times)
CALL GENERATE_SELECT_WORKLOAD(10000);
```

Once you see it's successfully executed, you are ready to start Exercise 3.1.


## Exercise 3.1 Generate index recommendations with Index Advisor

After completing these steps you will have successfully generated four index recommendations for your HANA Cloud instance with Index Advisor in **Recommendation App**.

1. **Open SAP BTP Cockpit** (Same with Exercise 1)
- Navigate to [SAP BTP Cockpit](https://tdct3ched1.accounts.ondemand.com/oauth2/authorize?response_type=code&scope=openid+email+profile&redirect_uri=https%3A%2F%2Femea.cockpit.btp.cloud.sap%2Flogin%2Fcallback&client_id=306ee77d-68d9-4398-ac62-1d07872563f9&state=EPkcyY---sTacmmvjflnPQ&code_challenge=fqM4tO2wlVaQLhRKfiqhS_2sXqA5WHfsG4QxvAc4oq4&code_challenge_method=S256).
- Login with your **_User Name_** and **_Password_**.
    - User Name: **_da263_0xx@education.cloud.sap_** (xx: your index)
    - Password: Provided in the PPT slide

2. **Access HANA Cloud Central** (Same with Exercise 1)
- Click **Instances and Subscriptions** in the leftmost navigation panel.
- Click **SAP HANA Cloud** in the Application list to open HANA Cloud Central.
<br>![](/exercises/ex1/images/01_00.png)
- In HANA Cloud Central, you can see your HANA Cloud instance. Click the instance and open **Overview** page.
<br>![](/exercises/ex1/images/01_01.png)

3. **Switch On the Index Advisor**
- Choose **Index** from **Recommendations** card.
<br>![](/exercises/ex3/images/03_00.png)

- Click **Switch On** and then click **Save**.
<br>![](/exercises/ex3/images/03_01.png)
<br>![](/exercises/ex3/images/03_02.png)

4. **Generate Recommendations**
- Now the advisor is switched on. Click **Generate Recommendations**.
<br>![](/exercises/ex3/images/03_03.png)

- You can see **Index** area is already selected. Click **Generate Recommendations** at the bottom.
<br>![](/exercises/ex3/images/03_04.png)
There is no specific configuration you need to do to switch on the Index Advisor unlike ECN Advisor or Partition Advisor. As soon as you request a recommendation generation, the advisor will start analyzing all the schemas and tables in your instance.

- The advisor is analyzing the tables and columns to generate recommendations to create or drop index. It takes around 1-2 minutes for this exercise.
<br>![](/exercises/ex3/images/03_05.png)

- Once the analysis is done and the advisor has identified tables that would benefit from creating or dropping index, there will be available recommendations generated. For this exercise, there are four recommendations generated. Two of them are create index recommendations and the other two are drop index recommendations.
<br>![](/exercises/ex3/images/03_06.png)


## Exercise 3.2 Apply index recommendations via SQL Console

As you have successfully generated index recommendations, you can apply the recommendations manually via SQL Console.

1.	**Open the SQL Console and connect to your instance**
- Go to the SQL Console by clicking the command line icon ![](/exercises/ex3/images/03_07.png) in the left navigation panel.

- Unless it's already connected to your instance with DBADMIN, select your instance **DA263__0XX** and click **Connect with Stored Credentials**.
<br>![](/exercises/ex3/images/03_07_a.png)
<br>![](/exercises/ex3/images/03_07_b.png)

2. **Check the performance before applying the recommendations**
- Delete any existing script in the console if there's any. Run the following SQL command to call the procedure and check the **Execution Elapsed Time** and **Execution CPU Time**. 
```SQL
CALL GENERATE_SELECT_WORKLOAD(10000);
```
<br>![](/exercises/ex3/images/03_08.png)
You will compare the performance after applying all recommendations.

3. **Review and apply the recommendations**
- Click the instances icon ![](/exercises/ex3/images/03_09.png) on the top of the navigation panel to go back to the instance list.

- Select your instance.
<br>![](/exercises/ex3/images/03_10.png)

- Go to the index recommendation list and click the first recommendation to check the details.
<br>![](/exercises/ex3/images/03_11.png)

- Review the details of the recommendation such as index information and copy the index SQL statement. This recommends to create an index on STATUS column in MOVIES table. Creating an index on a frequently accessed column improves query performance by allowing the database to quickly find and retrieve data without scanning the entire table. <ins>Please keep in mind that the sequence of the recommendations generated with your instance can be different from this guide.</ins>
<br>![](/exercises/ex3/images/03_12.png)

- Go back to the SQL Console and paste the copied SQL statement to create index.
<br>![](/exercises/ex3/images/03_13.png)

- Repeat the same task for the second recommendation which proposes to create index on GENRE column in MOVIES table.
<br>![](/exercises/ex3/images/03_14.png)

- Repeat the same task for the third recommendation to drop the index on GENRE column in MOVIES2 table. It's recommended as this index has never been used. Dropping the unused index improves database performance by reducing storage overhead and speeding up write operations.
<br>![](/exercises/ex3/images/03_15.png)

- Repeat it for the last recommendation to drop the index on STATUS column in MOVIES2 table.
<br>![](/exercises/ex3/images/03_16.png)

- After applying all four recommendations, you can see it from the statement history as below.
<br>![](/exercises/ex3/images/03_17.png)

4. **Validate the performance after applying the recommendations**

- Re-run the same SQL statement to call the procedure to check how much the performance has improved. 
```SQL
CALL GENERATE_SELECT_WORKLOAD(10000);
```

- You can see that the **Execution Elapsed Time** and **Execution CPU Time** have been significantly reduced.
<br>![](/exercises/ex3/images/03_18.png)


5. **Delete the existing recommendations**
- As the current Index Advisor doesn't support Apply feature, it cannot track the status of each recommendation. After you have applied the recommendations manually, the recommendations will stay as Available in the list. If you would like to clean up these recommendations, you can switch off the advisor instead.

- In the **Advisors** tab, click **Index Advisor** from the list.
<br>![](/exercises/ex3/images/03_20.png)

- Click **Switch Off** on the top right side.
<br>![](/exercises/ex3/images/03_21.png)

- Recommend retaining data for two weeks for your real instances, but for this exercise, choose **Remove data immediately** and click **Switch Off**.
<br>![](/exercises/ex3/images/03_22.png)

- The recommendations are deleted from the list.
<br>![](/exercises/ex3/images/03_23.png)

## Clean-up

**Stop your instance.**

- Go back to your Instance list.

- Click three dots icon in the Actions column and choose **Stop** from the list.

- Click **Stop Instance** in the pop-up window.

- Refresh the page to see if the instance has been stopped.

- Once it's stopped, close the HANA Cloud Central.

## Summary

You've successfully generated index recommendations for your instance and applied them manually via SQL Console.

You can find an asset about Index Advisor below.
- [Index Advisor Help Documentation](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-administration-guide/about-index-advisor?locale=en-US)


You've now completed all the hands-on exercise for **DA263: SAP HANA Cloud Advisor and Automation**.

Thank you for your participation.




