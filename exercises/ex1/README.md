# Exercise 1 - Elastic Compute Node Advisor and SAP Automation Pilot

In this exercise, we will configure **Elastic Compute Node Advisor (ECN Advisor)** and generate an ECN recommendation for a specific timeframe in **Recommendation App** from **HANA Cloud Central**. In addition, we will use **SAP Automation Pilot** to simplify and automate the provisioning and deprovisioning processes for ECNs.

## What is an Elastic Compute Node (ECN)?

An ECN is an instance type running an additional computeserver process (like an indexserver) which has only a temporary persistence and which can be easily added and dropped when required depending on the current workload state. It is useful to handle the known peak loads without upsizing your coordinator node permanently. You can find more information about the ECN [this guide](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-administration-guide/sap-hana-cloud-elastic-compute-node?locale=en-US).

## Exercise 1.1 Configure ECN Advisor and generate an ECN recommendation

After completing these steps you will have successfully generated an ECN recommendation for your HANA Cloud instance from HANA Cloud Central.

1. **Open SAP BTP Cockpit**
  - Navigate to [SAP BTP Cockpit](https://tdct3ched1.accounts.ondemand.com/oauth2/authorize?response_type=code&scope=openid+email+profile&redirect_uri=https%3A%2F%2Femea.cockpit.btp.cloud.sap%2Flogin%2Fcallback&client_id=306ee77d-68d9-4398-ac62-1d07872563f9&state=EPkcyY---sTacmmvjflnPQ&code_challenge=fqM4tO2wlVaQLhRKfiqhS_2sXqA5WHfsG4QxvAc4oq4&code_challenge_method=S256).
  - Login with your **_User Name_** and **_Password_**.

2. **Access HANA Cloud Central**
 - Click **Instances and Subscriptions** on the leftmost navigation panel.
 - Click **SAP HANA Cloud** in the Application list to open HANA Cloud Central.
 <br>![](/exercises/ex1/images/01_00.png)
 - In HANA Cloud Central, you can see your HANA Cloud instance.
 <br>![](/exercises/ex1/images/01_01.png)

3. **Switch on ECN Advisor**
  - When you click your HANA Cloud instance, you see **Overview** with many different tiles.
  <br>![](/exercises/ex1/images/01_01_01.png)
  - Scroll down a bit and click **Elastic Compute Node** from **Recommendations** App.
  <br>![](/exercises/ex1/images/01_02.png)
  - Click **Switch On**. You can see the resource usage over the past few days in the chart.
  <br>![](/exercises/ex1/images/01_03.png)
  - Add **Thresholds** for Memory and Compute. The default value is 80%, but in this exercise, we will set it as 70% for both.
  <br> You can also enable ECN Advisor to automatically generate a recommendation in a weekly basis, but we do not turn it on in this exercise.
  <br>![](/exercises/ex1/images/01_04.png)
  <br> Now the ECN Advisor is switched on.
  - Click **Generate Recommendations**.
  <br>![](/exercises/ex1/images/01_05.png)
  - You can see a recommendation topic selected as Elastic Compute Node. Define the **Analysis Timeframe** that the advisor will analyze your resource usage to generate a recommendation. The timeframe can be **_from 30 minutes up to 14 days_**. The latest time you can analyze is 20 minutes before the current time. And then click **Generate Recommendation** at the bottom.
  <br>![](/exercises/ex1/images/01_06.png)
  - 




## Exercise 1.2 Sub Exercise 2 Description

After completing these steps you will have...

1.	Enter this code.
```abap
DATA(lt_params) = request->get_form_fields(  ).
READ TABLE lt_params REFERENCE INTO DATA(lr_params) WITH KEY name = 'cmd'.
  IF sy-subrc <> 0.
    response->set_status( i_code = 400
                     i_reason = 'Bad request').
    RETURN.
  ENDIF.

```

2.	Click here.
<br>![](/exercises/ex1/images/01_02_0010.png)


## Summary

You've now ...

Continue to - [Exercise 2 - Exercise 2 Description](../ex2/README.md)

