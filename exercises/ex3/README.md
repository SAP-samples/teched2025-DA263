# Exercise 3 - Index Advisor

**Index Advisor** is an intelligent advisor to recommend index creation or index drop based on the heuristic rules. The current version of the advisor supports queries for single tables (filter queries). 

In this exercise, 

## Exercise 3.1 Generate index recommendations with Index Advisor

After completing these steps you will have successfully generated four index recommendations for your HANA Cloud instance with Index Advisor in **Recommendation App**.

1. **Open SAP BTP Cockpit** (Same with Exercise 1)
- Navigate to [SAP BTP Cockpit](https://tdct3ched1.accounts.ondemand.com/oauth2/authorize?response_type=code&scope=openid+email+profile&redirect_uri=https%3A%2F%2Femea.cockpit.btp.cloud.sap%2Flogin%2Fcallback&client_id=306ee77d-68d9-4398-ac62-1d07872563f9&state=EPkcyY---sTacmmvjflnPQ&code_challenge=fqM4tO2wlVaQLhRKfiqhS_2sXqA5WHfsG4QxvAc4oq4&code_challenge_method=S256).
- Login with your **_User Name_** and **_Password_**.


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

- The advisor is analyzing the tables and columns to generate recommendations to create or drop index.
<br>![](/exercises/ex3/images/03_05.png)

- Once the analysis is done and the advisor has identified tables that would benefit from creating or dropping index, there will be available recommendations generated. For this exercise, there are four recommendations generated. Two of them are create index recommendaitons and the other two are drop index recommendations.
<br>![](/exercises/ex3/images/03_06.png)


## Exercise 3.2 Apply index recommendations via SQL Console

As you have successfully generated index recommendations, you can apply the recommendations manually via SQL Console.

1.	**Open the SQL Console and connect to your instance**
- Go to the SQL Console by clicking the command line icon in the left navigation panel.
<br>![](/exercises/ex3/images/03_07.png)

- Select your instance **DA263__0XX** and click **Connect with Stored Credentials**.
<br>![](/exercises/ex3/images/03_07_a.png)
<br>![](/exercises/ex3/images/03_07_b.png)

2. **Check the performance before applying the recommendations**
- Run the following SQL command to call the procedure and check the **Execution Elaped Time**, **Execution CPU Time**, **Client Execution Time** and **Server Peak Memory**. 
```SQL
CALL GENERATE_SELECT_WORKLOAD(10000);
```
<br>![](/exercises/ex3/images/03_08.png)
You will compare the performance after applying all recommendations.

3. **Review and apply the recommendations**
- Click the instances icon on the top of the navigation panel to go back to the instance list.
<br>![](/exercises/ex3/images/03_09.png)

- Select your instance.
<br>![](/exercises/ex3/images/03_10.png)

- Go to the index recommendation list and click the first recommendation to check the details.
<br>![](/exercises/ex3/images/03_11.png)

- Review the details of the recommendation such as index information and copy the index SQL statement. This recommends to create an index on VAL1 column in MYTABLE to improve performance.
<br>![](/exercises/ex3/images/03_12.png)

- Go back to the SQL Command and paste the copied SQL statement to create index.
<br>![](/exercises/ex3/images/03_13.png)

- Repeat the same task for the second recommendation which proposes to create index on VAL2 column in MYTABLE.
<br>![](/exercises/ex3/images/03_14.png)

- Repeat the same task for the third recommendation to drop index on VAL1 column in MYTABLE2.
<br>![](/exercises/ex3/images/03_15.png)

- Repeat it for the last recommendation to drop index on VAL2 column in MYTABLE2.
<br>![](/exercises/ex3/images/03_16.png)

- After applying all four recommendations, you can see it from the statement history as below.
<br>![](/exercises/ex3/images/03_17.png)

4. **Validate the performance after applying the recommendations**

- Re-run the same SQL statement to call the procedure to check how much the performance has improved. You can see that the **Execution Elapsed Time**, **Execution CPU Time**, and **Client Execution Time** have been all significantly reduced. Meanwhile, the **Server Peak Memory** consumption has increased because of the newly created indexes.
<br>![](/exercises/ex3/images/03_18.png)

- You can try generating recommendations again to see if there are more recommendations generated.
<br>![](/exercises/ex3/images/03_19.png)

5. **Delete the existing recommendations**
- As the current Index Advisor doesn't support Apply feature, it cannot track the status of each recommendation. After you have applied the recommendations manually, the recommendations will still stay as Available in the list. If you would like to clean up these recommendations, you can switch off the advisor instead.

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

You've now completed all the hands-on exercise for **DA263: SAP HANA Cloud Advisor and Automation**.




