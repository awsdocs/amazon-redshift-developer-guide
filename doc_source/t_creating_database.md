# Step 1: Create a database<a name="t_creating_database"></a>

After you have verified that your cluster is up and running, you can create your first database\. This database is where you will actually create tables, load data, and run queries\. A single cluster can host multiple databases\. For example, you can have a TICKIT database and an ORDERS database on the same cluster\.

After you connect to the initial cluster database, the database you created when you launched the cluster, you use the initial database as the base for creating a new database\.

For example, to create a database named **tickit**, issue the following command in your SQL client tool: 

```
create database tickit;
```

For this exercise, we'll accept the defaults\. For information about more command options, see [CREATE DATABASE](r_CREATE_DATABASE.md) in the SQL Command Reference\.

After you have created the TICKIT database, you can connect to the new database from your SQL client\. Use the same connection parameters as you used for your current connection, but change the database name to `tickit`\. 

You do not need to change the database to complete the remainder of this tutorial\. If you prefer not to connect to the TICKIT database, you can try the rest of the examples in this section using the default database\.