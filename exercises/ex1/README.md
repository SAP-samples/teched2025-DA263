# Exercise 1 - Elastic Compute Node Advisor and Automation Pilot

In this exercise, we will configure an Elastic Compute Node Advisor (ECN Advisor) and generate an ECN recommendation for a specific timeframe in Recommendation App from HANA Cloud Central. In addition, we will use an Automation Pilot to simplify and automate the provisioning and deprovisioning processes for ECNs.
An ECN is an instance type running an additional computeserver process (like an indexserver) which has only a temporary persistence and which can be easily added and dropped when required depending on the current workload state. It is useful to handle the known peak loads.

## Exercise 1.1 Sub Exercise 1 Description

After completing these steps you will have 

1. Open SAP BTP Cockpit and login with your User Name and Password.
Add link to BTP Cockpit
And click Instances and Subscriptions and in the Subscriptions list, click SAP HANA Cloud application to open HANA Cloud Central.
<br>![](/exercises/ex1/images/01_00.png)

Then in HANA Cloud Central, you can see your HANA Cloud instance.

2.	Insert this line of code.
```abap
response->set_text( |Hello World! | ). 
```



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

