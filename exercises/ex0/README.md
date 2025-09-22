# Getting Started

This session covers configuring the Elastic Compute Node Advisor, Partition Advisor, and Index Advisor to generate database optimization recommendations, and explore multiple ways to apply those recommendations. 

<ins>**For the hands-on workshop, all prerequisites listed below are already in place.**</ins> This information is for users who want to complete the exercise on their own outside the session.

## Prerequisites for Exercise 1

- An SAP BTP subaccount

- An SAP HANA Cloud instance (non trial/free-tier instance)
  - The instance must have <ins>at least 5 vCPUs</ins> to use ECN Advisor
  - The instance should allow connections from specific IP addresses and IP ranges. Add IP Addresses of your BTP region ([Service Availability](https://help.sap.com/docs/automation-pilot/automation-pilot/what-is-sap-automation-pilot?locale=en-US)). For the hands-on session, your instance already has the following connection setup.
  <br>![](/exercises/ex1/images/00_ap00.png)

- Access to HANA Cloud Central

- SAP HANA Cloud Administrator role collection
  - to configure ECN Advisor
  - to add, edit, or remove ECN node(s)

- SAP HANA Cloud Viewer role collection
  - to view ECN Advisor information

- Access to SAP Automation Pilot Service

- **WORKLOAD ADMIN** privilege
  - to route workload to an ECN using workload classes

- At least one Workload Class must be available in your instance, before the analysis timeframe, for routing queries to the ECN

- Further information on [scope and limitations of ECN Advisor](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-administration-guide/elastic-compute-node-advisor?locale=en-US)


## Prerequisites for Exercise 2

- An SAP BTP subaccount

- An SAP HANA Cloud instance (non trial/free-tier instance)

- Access to HANA Cloud Central

- SAP HANA Cloud Aministrator role collection
  - to switch on Partition Advisor

- SAP HANA Cloud Viewer role collection
  - to view Partition Advisor information

- **Partiton Advisor User** with following privileges:
  - **CATALOG READ** system privilege for generating recommendations
  - **PARTITION ADMIN** system privilege for applying recommendations
  - **SELECT** privilege ON SCHEMA **‘SCHEMA NAME’** to enable the advisor user to see <ins>a schema and the tables contained in it</ins>
  - (Optional) **SELECT** privilege ON **‘TABLE NAME’** to enable the advisor user to see and generate recommendations for <ins>a specific table only</ins>


## Prerequisites for Exercise 3

- An SAP BTP subaccount
- An SAP HANA Cloud instance (non trial/free-tier instance)



## Summary

Now you are ready to start the exercises.

Continue to - [Exercise 1 - Elastic Compute Node Advisor and SAP Automation Pilot](../ex1/README.md)
