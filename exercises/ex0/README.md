# Getting Started

This hands-on workshop session will transform how you optimize your SAP HANA Cloud databases. You will explore the intelligent advisors available in HANA Cloud Central, where you'll learn to configure and leverage the Elastic Compute Node Advisor, Partition Advisor, and Index Advisor.

This interactive session is designed to give you practical experience in generating intelligent database optimization recommendations that can improve your system performance. You will dive deep into the configuration process for each advisor, and learn multiple proven approaches for implementing these recommendations.

<ins>**For the hands-on workshop, most of prerequisites listed below are already in place.**</ins> This information is for users who want to complete the exercise outside the session with their own system environment. You can read the following prerequisites for your information, but you are <ins>**not**</ins> required to do any actions here.

## Prerequisites for Exercise 1

- An SAP BTP subaccount

- An SAP HANA Cloud instance (non trial/free-tier instance)
  - The instance must have <ins>at least 5 vCPUs</ins> to use ECN Advisor
  - The instance should allow connections from specific IP addresses and IP ranges. Add IP Addresses of your BTP region ([Service Availability](https://help.sap.com/docs/automation-pilot/automation-pilot/what-is-sap-automation-pilot?locale=en-US)). For the hands-on session, your instance already has the following connection setup.
  <br>![](/exercises/ex0/images/00_ap00.png)

- Access to HANA Cloud Central

- SAP HANA Cloud Administrator role collection
  - to configure ECN Advisor
  - to add, edit, or remove ECN node(s)

- SAP HANA Cloud Viewer role collection
  - to view ECN Advisor information

- Access to SAP Automation Pilot Service

- **WORKLOAD ADMIN** privilege
  - to route workload to an ECN using workload classes

- At least one **Workload Class** must be available in your instance, before the analysis timeframe, for routing queries to the ECN

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
- SAP HANA Cloud Aministrator role collection
  - to switch on Index Advisor

- SAP HANA Cloud Viewer role collection
  - to view Index Advisor information

- **CATALOG READ** system privilege for generating recommendations

- **SELECT** privilege on the table or the schema containing the table for generating recommendations

- One of the following must be true for applying recommendations:
  - You <ins>own</ins> the table referenced by the index.
  - You <ins>own</ins> the schema that contains the table referenced by the index.
  - You have the **INDEX** object-level privilege on the table referenced by the index.
  - You have the **INDEX** schema-level privilege on the schema containing the table referenced by the index.


## Summary

Now you are ready to start the exercises.

Continue to - [Exercise 1 - Elastic Compute Node Advisor and SAP Automation Pilot](../ex1/README.md)
