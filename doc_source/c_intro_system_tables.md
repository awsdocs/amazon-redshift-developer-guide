# System tables and views<a name="c_intro_system_tables"></a>

Amazon Redshift has many system tables and views that contain information about how the system is functioning\. You can query these system tables and views the same way that you would query any other database tables\. This section shows some sample system table queries and explains: 
+ How different types of system tables and views are generated
+ What types of information you can obtain from these tables
+ How to join Amazon Redshift system tables to catalog tables
+ How to manage the growth of system table log files

Some system tables can only be used by AWS staff for diagnostic purposes\. The following sections discuss the system tables that can be queried for useful information by system administrators or other database users\. 

**Note**  
System tables are not included in automated or manual cluster backups \(snapshots\)\. STL log views only retain approximately two to five days of log history, depending on log usage and available disk space\. If you want to retain the log data, you will need to periodically copy it to other tables or unload it to Amazon S3\.