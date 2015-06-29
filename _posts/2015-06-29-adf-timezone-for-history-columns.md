---
title: ADF - Time Zone for History Columns
tags: [Time Zone, History Column, ADF Business Components, ADF]
excerpt: As a special note of the time zone in relation to the ADF Business Components DATE history columns, This post continues the series of posts covering the time zone handling in Oracle ADF applications.
---

In my last post of the time zone series - [Time Zone for Oracle JDBC Driver]({% post_url 2015-03-23-timezone-for-oracle-jdbc-driver %}), I introduced how the Oracle JDBC Driver processes date values with regards to time zones. As a special note, in this post, I will introduce how time zones affect the ADF Business Components 'Date' history columns, specifically, "Created On" and "Modified On" columns.

Here's the description from the official document (see the resources below):

> * **Created On**: This attribute is populated with the time stamp of when the row was created. The time stamp is obtained from the database.
> * **Modified On**: This attribute is populated with the time stamp whenever the row is updated/created.

So, the time stamp is obtained from the database (when there is a connection), rather than JVM in which ADF is running. Basically, an appropriate query will be executed in the database to get the current database time; then the value is returned by JDBC and converted to appropriate Java Date type. In this process, the rules introduced in my last post apply.

For example, for Oracle Database, the query (see resources below) would be like this:

```sql
select sysdate from dual
```

Whenever an entity object is about to initialize or update its history columns, the current transaction object is inquiried about the current database time. The transaction object executes the query statement and returns the timestamp obtained as the result from the database (for performance benefit, the query statement is executed only for the first time, and then a difference between the database time and JVM time is saved and added to the JVM time to get a result for a subsequent request).

Now it's clear, to get correct history columns, we need make sure both the database time zone  and JVM time zone are configured correctly. Typically, both of them should be configured to be UTC time zone, and then we can use converters for UI components to display date values or accept user input for date values in any other time zones appropriate, as introduced in my another post, [ADF - Time Zone for af:convertDateTime]({% post_url 2015-03-20-adf-timezone-for-convertdatetime %}).

## Resources

* [4.10.12 How to Track Created and Modified Dates Using the History Column](https://docs.oracle.com/middleware/1213/adf/develop/adf-bc-entity-objects.htm#ADFFD201)
* [oracle.jbo.server.OracleSQLBuilderImpl.getDbTimeQuery()](https://docs.oracle.com/middleware/1213/adf/api-reference-model/oracle/jbo/server/OracleSQLBuilderImpl.html#getDbTimeQuery())
* [oracle.jbo.server.DBTransactionImpl.getCurrentDbTime()](https://docs.oracle.com/middleware/1213/adf/api-reference-model/oracle/jbo/server/DBTransactionImpl.html#getCurrentDbTime())