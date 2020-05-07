# Tuning query performance<a name="c-optimizing-query-performance"></a>

Amazon Redshift uses queries based on structured query language \(SQL\) to interact with data and objects in the system\. Data manipulation language \(DML\) is the subset of SQL that you use to view, add, change, and delete data\. Data definition language \(DDL\) is the subset of SQL that you use to add, change, and delete database objects such as tables and views\.

Once your system is set up, you typically work with DML the most, especially the [SELECT](r_SELECT_synopsis.md) command for retrieving and viewing data\. To write effective data retrieval queries in Amazon Redshift, become familiar with SELECT and apply the tips outlined in [Amazon Redshift best practices for designing tables](c_designing-tables-best-practices.md) to maximize query efficiency\.

To understand how Amazon Redshift processes queries, use the [Query processing](c-query-processing.md) and [Analyzing and improving queries](c-query-tuning.md) sections\. Then you can apply this information in combination with diagnostic tools to identify and eliminate issues in query performance\.

To identify and address some of the most common and most serious issues you are likely to encounter with Amazon Redshift queries, use the [Troubleshooting queries](queries-troubleshooting.md) section\.

**Topics**
+ [Query processing](c-query-processing.md)
+ [Analyzing and improving queries](c-query-tuning.md)
+ [Troubleshooting queries](queries-troubleshooting.md)