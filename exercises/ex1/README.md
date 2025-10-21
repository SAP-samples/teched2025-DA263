# Exercise 1 - Elastic Compute Node Advisor and SAP Automation Pilot

In this exercise, we will configure **Elastic Compute Node Advisor (ECN Advisor)** and generate an ECN recommendation for a specific timeframe in **Recommendation App** from **HANA Cloud Central**. In addition, we will use **SAP Automation Pilot** to simplify and automate the provisioning and deprovisioning processes for ECNs.

## What are Elastic Compute Node (ECN) and ECN Advisor?

An **Elastic Compute Node (ECN)** is a read-only compute node where the compute-intensive workloads can be distributed. It's basically equivalent to the indexserver but no persistency to store data permanently, and can be easily added and dropped when required depending on the current workload state. It is most suitable for compute-intensive OLAP or read-only peak workloads. It can effectively and flexibly handle upcoming workload peaks while keeping your coordinator (indexserver) unchanged. For example, an Intelligent Data Application running on SAP HANA Cloud can leverage ECNs to effectively address compute-intentive workload peaks, such as quarter-end jobs. You can find more information about the ECN in [this guide](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-administration-guide/sap-hana-cloud-elastic-compute-node?locale=en-US).

The **ECN Advisor** helps you identify workloads that drive peak CPU or memory within a specified time window and recommend when to provision and deprovision ECNs. The selection criteria are as below:
- Significant CPU or memory consumption
- Predictable patterns (daily, weekly, or monthly)
- Read-only (no DML, no updatable SQLScript)

The ECN Advisor will be continuously enhanced with more advanced automation and usability features.

## What is SAP Automation Pilot?

**SAP Automation Pilot** is a cloud-based automation service within the SAP Business Technology Platform (BTP) DevOps portfolio. As a low-code/no-code engine, it is designed to simplify and automate complex manual processes and tasks, enhancing efficiency and reducing operational overhead by designing and running Ops automation flows. You can find further information about Automation Pilot [here](https://help.sap.com/docs/automation-pilot/automation-pilot/what-is-sap-automation-pilot?locale=en-US), and please be noted that this service is <ins>**not yet**</ins> available in all regions.

## Exercise 1.1 Configure ECN Advisor and generate an ECN recommendation

After completing these steps you will have successfully generated an ECN recommendation for your HANA Cloud instance from HANA Cloud Central.

1. **Open SAP BTP Cockpit**
- Navigate to [SAP BTP Cockpit](https://emea.cockpit.btp.cloud.sap/cockpit?idp=tdct3ched1.accounts.ondemand.com#/globalaccount/4c772782-0751-42ee-93c3-897452fdcb63/subaccount/27eb38bc-fdf9-4055-9a8d-6d30ea5f2b8f/service-instances).
- Login with your **_User Name_** and **_Password_**.
    - User Name: **_da263-0xx@education.cloud.sap_** (xx: your index)
    - Password: **_Provided in the PPT slide_**


2. **Access HANA Cloud Central**
- Click **Instances and Subscriptions** in the leftmost navigation panel.
- Click **SAP HANA Cloud** in the Application list to open HANA Cloud Central.
<br>![](/exercises/ex1/images/01_00.png)
- In HANA Cloud Central, you can see your HANA Cloud instance.
<br>![](/exercises/ex1/images/01_01.png)


3. **Check Resource Consumption**
- When you click your HANA Cloud instance, you see an **Overview** page with many different tiles.
<br>![](/exercises/ex1/images/01_01_01.png)

- Click **Compute** card.

- When you choose **24 Hours** on top, you will see some compute spikes occurred.
<br>![](/exercises/ex1/images/01_17.png)


4. **Switch on ECN Advisor**
- Go back to **Overview** page by clicking your instance name on top next to Usage Monitor.

- Find a **Recommendations** card and click **Elastic Compute Node**.
<br>![](/exercises/ex1/images/01_02.png)  


- Click **Switch On**. You can see the resource usage over the past few days in the chart.
<br>![](/exercises/ex1/images/01_03.png)  


- Add **Thresholds** for Memory and Compute. The default value is **80%**, and you can add the thresholds that are suitable for your use case. In this exercise, we will set it as **_70%_** for both **Memory** and **Compute**.
<br>![](/exercises/ex1/images/01_04.png)
<br> Now the ECN Advisor is **switched on**.  


5. **Generate Recommendations**
- Click **Generate Recommendations**.
<br>![](/exercises/ex1/images/01_05.png)  


- You can see a recommendation topic selected as **Elastic Compute Node**. Define the **Analysis Timeframe** that the advisor will analyze your resource usage to generate a recommendation. The time frame must be <u>*between 30 minutes and 24 hours*</u> in length. It must end <u>*at least 20 minutes before*</u> the recommendation request, and must be <u>*within the last 14 days*</u>. 
In this exercise, you keep it as 1 day as it has been already selected by default. Click **Generate Recommendation** at the bottom.
<br>![](/exercises/ex1/images/01_06.png)


- The ECN Advisor is **Generating** a recommendation. You can click Refresh icon at the top right side to see if the recommendation has been generated.
<br>![](/exercises/ex1/images/01_07.png)


- Once the recommendation has been generated, you see **Available** in the **Status** column.
<br>![](/exercises/ex1/images/01_08.png)


6. **Review Recommendation Details** 
- Click the available recommendation in the list. There is a **Recommended Configuration** and an **Explanation** field. In the **Recommended Configuration**, you can see the recommended schedule and configuration for the ECN as well as expected capacity units that will be consumed with the ECN. You can click **Workload Class** to see which workload class is recommended to be routed to the ECN.
<br>![](/exercises/ex1/images/01_09.png)
<br>![](/exercises/ex1/images/01_10.png)


- In the **Explanation** field, you can see the memory and compute usage chart and the explanation at the bottom.
<br>![](/exercises/ex1/images/01_11.png)


- (Optional) On the top right side of the recommendation, there is a **Provide Feedback** button to share your feedback on this recommendation. You can provide your evaluation on if and how this specific recommendation was helpful for you. This information will be kept confidential and used only for analysis by SAP internal teams to improve the advisors.
<br>![](/exercises/ex1/images/01_12_fb.png)

<br>Now you have successfully generated the ECN recommendation. You can move on to the next step to provision and deprovision an ECN as recommended by the advisor.


## Exercise 1.2 Provision and deprovision an ECN with SAP Automation Pilot

You can provision and deprovision the ECN and route the workload class to the ECN within HANA Cloud Central. However, to make this process more easily repeatable, in this practice, you will learn how to make use of **SAP Automation Pilot**.

1. **Configure the HANA Cloud instance to allow connections for Automation Pilot**
- In the Instance Overview page at HANA Cloud Central, click **Manage Configuration** on top.

- Go to **Connections**, choose **Allow specific IP addresses and IP ranges** option and then add the following 6 IP addresses.
```
18.192.167.80,
18.158.182.81,
18.192.94.153,
18.159.0.107,
18.159.145.225,
3.122.189.245
```
You can click need to add the IP address in the table view one by one by clicking **Add** button as you see in the screenshot below.
<br>![](/exercises/ex1/images/00_ap00.png)

Or, in a simpler way, you can switch to the syntax view and copy and paste the 6 IP addresses like below.
<br>![](/exercises/ex1/images/00_ap01.png)

- Click **Review and Save**.

- Choose **Apply changes without a restart** option for the sake of time and click **Save Changes**.


2. **Open Automation Pilot from SAP BTP Cockpit**
- Go back to SAP BTP Cockpit.
- Click **Automation Pilot** from Application list from **Subscriptions**.
<br>![](/exercises/ex1/images/01_ap01.png)


3.	**Import an ECN HANA Cloud Other Environment Catalog**

With the **Automation Pilot**, you can run predefined commands or create custom commands. For ECN provisioning and deprovisioning in HANA Cloud Other Environment instance, we have published a catalog with predefined commands to make it easy to use. This catalog is not included in **Provided Catalogs** of the service, but you will import the prepared catalog through the following steps.

- Go to **My Catalogs** and click **Import** button on the top right side.
<br>![](/exercises/ex1/images/01_ap02.png)

- Click [here](https://github.com/SAP-samples/automation-pilot-examples/blob/main/hana-ecn-other/ecncatalog_otherenv.json) and copy raw file. This repository contains a curated collection of example Automation Pilot commands and what you see now is for ECN scenario.
<br>![](/exercises/ex1/images/01_ap03.png)

- Paste the copied raw file into the bottom field and click import. This is a json file that includes all the necessary parameters for ECN provisioning and deprovisioning.
<br>![](/exercises/ex1/images/01_ap04.png)

- You can now see the imported ECN catalog in your catalog list.
<br>![](/exercises/ex1/images/01_ap05.png)


4. **Edit Inputs of the Catalog**
- Click **Inputs** in the catalog.
<br>![](/exercises/ex1/images/01_ap19.png)

- Click **HanaCloudOtherEnvironments**.
<br>![](/exercises/ex1/images/01_ap07.png)

- You can see the list of **Keys** in **Content** field. Edit each key by clicking a Pencil icon.
<br>![](/exercises/ex1/images/01_ap08.png)

- For the first key, you need to add **connectionUrl**. Access **HANA Cloud Central** to **Copy SQL Endpoint** of your instance.
<br>![](/exercises/ex1/images/01_ap06.png)

- Add the copied SQL Endpoint in the value after <ins>**jdbc:sap://**</ins> and save it.
<br>![](/exercises/ex1/images/01_ap09.png)

- Update **dbPassword** to your instance password.
<br>![](/exercises/ex1/images/01_ap10.png)
<br>![](/exercises/ex1/images/01_ap11.png)
Your DB Password: **_Welcome1_**

- Update **instanceID** to your instance ID. You can copy it from **HANA Cloud Central** again.
<br>![](/exercises/ex1/images/01_ap12.png)
<br>![](/exercises/ex1/images/01_ap13.png)
<br>![](/exercises/ex1/images/01_ap14.png)

- Update **serviceKey**. Copy JSON from **BTP Cockpit** and paste it as below.
<br>![](/exercises/ex1/images/01_ap15.png)
<br>![](/exercises/ex1/images/01_ap16.png)
<br>![](/exercises/ex1/images/01_ap17.png)
<br>![](/exercises/ex1/images/01_ap18.png)

- Now you go back to **Inputs** list and click **HanaCloudServiceKey**.
<br>![](/exercises/ex1/images/01_ap20.png)

- Update **serviceKey** <ins>again</ins> by copying and pasting JSON from **BTP Cockpit**.
<br>![](/exercises/ex1/images/01_ap21.png)
<br>![](/exercises/ex1/images/01_ap22.png)


5. **Provision ECN with Commands**
- Click **Commands** from the imported ECN HANA Cloud Other Environment catalog.
<br>![](/exercises/ex1/images/01_ap23.png)

- Click **ProvisionHanaCloudElasticComputeNode** from the list of commands.
<br>![](/exercises/ex1/images/01_ap24.png)

- Scroll down and select **EnableWorkloadClass** in the **Executors** and edit the statement and the name of the workload class which needs to be routed to the ECN. <ins>You will find which statement to put in the field in the following steps.</ins> 
<br>![](/exercises/ex1/images/01_ap25.png)

To check the workload name, go to **HANA Cloud Central** and under the **Elastic Compute Node** page, you can find **Manage Workload Classes** button. When you click it, you will see the list of **Workload Classes**.
<br>![](/exercises/ex1/images/01_ap26.png)
<br>![](/exercises/ex1/images/01_ap27.png)

Come back to **Automation Pilot** and edit the statement to route the selected workload class to a new ECN that will be provisioned by setting the routing location hint.
You can copy and paste the following statement.
```sql
ALTER WORKLOAD CLASS "WLC1" SET 'ROUTING LOCATION HINT'='ecn1';
```
<br>![](/exercises/ex1/images/01_ap28.png)


- And now you are ready to **Trigger** the command. Click **Trigger** on the top right side.
<br>![](/exercises/ex1/images/01_ap29.png)

- Select **HanaCloudOtherEnvironments** for **Inputs**.
<br>![](/exercises/ex1/images/01_ap30.png)

- For the rest of the **Parameters**, you can take the recommended configuration that the advisor provided.
Add **cpu**, **memory**, **storage** as recommended, and add a **nodeName**. Make sure you are using the same node name as you put in the **EnableWorkloadClass** executor earlier. And click **Trigger**.
<br>![](/exercises/ex1/images/01_ap31.png)
<br>![](/exercises/ex1/images/01_ap32.png)

- Now the command is being executed and you can check the progress. It will take around 3-4 minutes.
<br>![](/exercises/ex1/images/01_ap33.png)

- You will see **Finished** status when it's completed. It means the ECN is successfully provisioned and the selected workload class is set to be routed to the provisioned ECN.
<br>![](/exercises/ex1/images/01_ap34.png)

- To check, you can go to **HANA Cloud Central** and in **Elastic Compute Node** page, hit the refresh button and then you see the <u>**ecn1**</u> created in the **Current Running ECNs** list. You can also click **1 workload classes** and see now the **WLC1** has a routing location set as **ecn1**.
<br>![](/exercises/ex1/images/01_ap35.png)
<br>![](/exercises/ex1/images/01_ap36.png)

6. **Run the workload with ECN**

- Open the SQL Console.
<br>![](/exercises/ex1/images/01_15.png)

- Connect to **ECN User** by running the following script.
```SQL
CONNECT ECNUSER PASSWORD Ecnadvisor1;
```

- Run the following script to call the pre-defined procedure **CALL_SPIKE()** for the workload. It would take 4-5 minutes for this statement to run.
```SQL
CALL CALL_SPIKE();
```

- Once it's executed, go back to **Instance Overview** page and click **Compute** card.

- Scroll down a bit and open **Elastic Compute Node (ecn1)** part. You can verify that the workload was run with the **ecn1** from the spike observed in the Compute graph.
<br>![](/exercises/ex1/images/01_16.png)

7. **Deprovision ECN with Commands**
- Choose **DeprovisionHanaCloudElasticComputeNode** from the **Commands** list.
<br>![](/exercises/ex1/images/01_ap37.png)

- Edit **statement** parameter to update the workload class name from **DisableWorkloadClass** Executor. This executor will disable the workload class.
<br>![](/exercises/ex1/images/01_ap38.png)
<br>![](/exercises/ex1/images/01_ap39.png)

- Trigger the command. Select **HanaCloudOtherEnvironments** for **Inputs** and add the ECN name, <u>**ecn1**</u> in the **nodeName** field. And click **Trigger**.
<br>![](/exercises/ex1/images/01_ap40.png)

- The **Automation Pilot** is disabling the workload class and deprovisioning the ECN.
<br>![](/exercises/ex1/images/01_ap41.png)

- You can check the the execution is in progress from **HANA Cloud Central** as well.
<br>![](/exercises/ex1/images/01_ap42.png)

- Now the ECN is successfully deprovisioned.
<br>![](/exercises/ex1/images/01_ap43.png)

- In **HANA Cloud Central**, you can check there is no running ECN anymore.
<br>![](/exercises/ex1/images/01_ap44.png)


8. **Easily Repeat Commands with Automation Pilot**
- To simplify repeating the provisioning and deprovisioning of ECN with the same configuration in a regular basis, you can go to **Executions** list and select the command that you have executed before and simply **Retrigger** the command.
<br>![](/exercises/ex1/images/01_ap45.png)
<br>![](/exercises/ex1/images/01_ap46.png)


## Clean-up

**Switch off ECN Advisor**

- Go to **Advisors** tab in the Recommendation App.

- Click the three dots on the top right side and select **Switch Off**.
<br>![](/exercises/ex1/images/01_13.png)

- Choose **Remove data immediately** and click **Switch Off**.
<br>![](/exercises/ex1/images/01_14.png)

The ECN Advisor is off now and all recommendation data has been deleted.
You can go back to **Instance Overview** page.


## Summary

You've learned how to configure ECN Advisor to generate recommendations for your workload, and how to provision and deprovision ECNs more simply and automatically with Automation Pilot.

You can find more assets about ECN and ECN Advisor below.
- Blog: [Harnessing Dynamic Elasticity (Elastic Compute Node) for Smarter Scaling in SAP HANA Cloud](https://community.sap.com/t5/technology-blog-posts-by-sap/harnessing-dynamic-elasticity-elastic-compute-node-for-smarter-scaling-in/ba-p/14016836)
- Tutorial: [Use an Elastic Compute Node (ECN) for Scheduled Workloads](https://developers.sap.com/tutorials/hana-cloud-ecn..html#dab4c6d2-91f5-4b55-be9f-16a0ee7a50e2)


Continue to - [Exercise 2 - Partition Advisor](../ex2/README.md)

