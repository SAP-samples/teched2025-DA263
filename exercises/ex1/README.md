# Exercise 1 - Elastic Compute Node Advisor and SAP Automation Pilot

In this exercise, we will configure **Elastic Compute Node Advisor (ECN Advisor)** and generate an ECN recommendation for a specific timeframe in **Recommendation App** from **HANA Cloud Central**. In addition, we will use **SAP Automation Pilot** to simplify and automate the provisioning and deprovisioning processes for ECNs.

## What is Elastic Compute Node (ECN)?

An ECN is an instance type running an additional computeserver process (like an indexserver) which has only a temporary persistence and which can be easily added and dropped when required depending on the current workload state. It is useful to handle the known peak loads without upsizing your coordinator node permanently. You can find more information about the ECN in [this guide](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-administration-guide/sap-hana-cloud-elastic-compute-node?locale=en-US).

## What is SAP Automation Pilot?

SAP Automation Pilot is a cloud-based automation service within the SAP Business Technology Platform (BTP) DevOps portfolio. As a low-code/no-code engine, it is designed to simplify and automate complex amnual processes and tasks, enhancing efficiency and reducing operational overhead by designing and running Ops automation flows. You can find further information about Automation Pilot [here](https://help.sap.com/docs/automation-pilot/automation-pilot/what-is-sap-automation-pilot?locale=en-US), and please be noted that this service is <u>**not yet**</u> available in all regions.

## Exercise 1.1 Configure ECN Advisor and generate an ECN recommendation

After completing these steps you will have successfully generated an ECN recommendation for your HANA Cloud instance from HANA Cloud Central.

1. **Open SAP BTP Cockpit**
- Navigate to [SAP BTP Cockpit](https://tdct3ched1.accounts.ondemand.com/oauth2/authorize?response_type=code&scope=openid+email+profile&redirect_uri=https%3A%2F%2Femea.cockpit.btp.cloud.sap%2Flogin%2Fcallback&client_id=306ee77d-68d9-4398-ac62-1d07872563f9&state=EPkcyY---sTacmmvjflnPQ&code_challenge=fqM4tO2wlVaQLhRKfiqhS_2sXqA5WHfsG4QxvAc4oq4&code_challenge_method=S256).
- Login with your **_User Name_** and **_Password_**.


2. **Access HANA Cloud Central**
- Click **Instances and Subscriptions** in the leftmost navigation panel.
- Click **SAP HANA Cloud** in the Application list to open HANA Cloud Central.
<br>![](/exercises/ex1/images/01_00.png)
- In HANA Cloud Central, you can see your HANA Cloud instance.
<br>![](/exercises/ex1/images/01_01.png)


3. **Switch on ECN Advisor**
- When you click your HANA Cloud instance, you see an **Overview** page with many different tiles.
<br>![](/exercises/ex1/images/01_01_01.png)  


- Find a **Recommendations** App tile and click **Elastic Compute Node**.
<br>![](/exercises/ex1/images/01_02.png)  


- Click **Switch On**. You can see the resource usage over the past few days in the chart.
<br>![](/exercises/ex1/images/01_03.png)  


- Add **Thresholds** for Memory and Compute. The default value is 80%, but in this exercise, we will set it as **_70%_** for both Memory and Compute.
<br> You can also enable ECN Advisor to automatically generate a recommendation in a weekly basis, but we <u>**do not use**</u> this feature in this exercise.
<br>![](/exercises/ex1/images/01_04.png)
<br> Now the ECN Advisor is **switched on**.  


4. **Generate Recommendations**
- Click **Generate Recommendations**.
<br>![](/exercises/ex1/images/01_05.png)  


- You can see a recommendation topic selected as **Elastic Compute Node**. Define the **Analysis Timeframe** that the advisor will analyze your resource usage to generate a recommendation. The time frame must be <u>*between 30 minutes and 14 days*</u> in length. It must end <u>*at least 20 minutes before*</u> the recommendation request, and must be <u>*within the last 14 days*</u>. Once you have selected the timeframe, click **Generate Recommendation** at the bottom.
<br>![](/exercises/ex1/images/01_06.png)  


- The ECN Advisor is **Generating** a recommendation. You can click Refresh icon at the top right side to see if the recommendation has been generated.
<br>![](/exercises/ex1/images/01_07.png)


- Once the recommendation has been generated, you see **Available** in the **Status** column.
<br>![](/exercises/ex1/images/01_08.png)


5. **Review Recommendation Details** 
- There is a **Recommended Configuration** and an **Explanation** field. In the **Recommended Configuration**, you can see the recommended schedule and configuration for the ECN as well as expected capacity units that will be consumed with the ECN. You can click **Workload Class** to see which workload class is recommended to be routed to the ECN.
<br>![](/exercises/ex1/images/01_09.png)
<br>![](/exercises/ex1/images/01_10.png)


- In the **Explanation** field, you can see the memory and compute usage chart and the explanation at the bottom.
<br>![](/exercises/ex1/images/01_11.png)


- (Optional) On the top right side of the recommendation, there is a **Provide Feedback** button to share your feedback on this recommendation. Your feedback is always appreciated and helpful to improve our advisor.
<br>![](/exercises/ex1/images/01_12_fb.png)

<br>Now you have successfully generated the ECN recommendation. You can move on to the next step to provision and deprovision an ECN as recommended by the advisor.


<br>

## Exercise 1.2 Provision and deprovision an ECN with SAP Automation Pilot

You can provision and deprovision the ECN and route the workload class to the ECN within HANA Cloud Central. However, to make this process more easily repeatable, in this practice, you will learn how to make use of **SAP Automation Pilot**.


1. **Open Automation Pilot from SAP BTP Cockpit**
- Go back to [SAP BTP Cockpit](https://tdct3ched1.accounts.ondemand.com/oauth2/authorize?response_type=code&scope=openid+email+profile&redirect_uri=https%3A%2F%2Femea.cockpit.btp.cloud.sap%2Flogin%2Fcallback&client_id=306ee77d-68d9-4398-ac62-1d07872563f9&state=EPkcyY---sTacmmvjflnPQ&code_challenge=fqM4tO2wlVaQLhRKfiqhS_2sXqA5WHfsG4QxvAc4oq4&code_challenge_method=S256).
- Click **Automation Pilot** from Application list.
<br>![](/exercises/ex1/images/01_ap01.png)

2.	**Import an ECN HANA Cloud Other Environment Catalog**
- Go to **My Catalogs** and click **Import** button on the top right side.
<br>![](/exercises/ex1/images/01_ap02.png)

- Click [here](https://github.com/SAP-samples/automation-pilot-examples/blob/main/hana-ecn-other/ecncatalog_otherenv.json) and copy raw file.
<br>![](/exercises/ex1/images/01_ap03.png)

- Paste the copied raw file into the bottom field and click import.
<br>![](/exercises/ex1/images/01_ap04.png)

- You can now see the imported ECN catalog in your catalog list.
<br>![](/exercises/ex1/images/01_ap05.png)

3. **Edit Inputs for the Catalog**
- Click **Inputs** in the catalog.
- Click **HanaCloudOtherEnvironments**.
<br>![](/exercises/ex1/images/01_ap07.png)
- You can see the list of **Keys** in **Content** field. Edit the keys by clicking a Pencil icon.
For the first key, you need to add **connectionUrl**.
<br>![](/exercises/ex1/images/01_ap08.png)
- Access **HANA Cloud Central** to **Copy SQL Endpoint** of your instance.
<br>![](/exercises/ex1/images/01_ap06.png)






## Summary

You've now ...

Continue to - [Exercise 2 - Exercise 2 Description](../ex2/README.md)

