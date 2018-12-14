# Loading Data<a name="t_Loading_data"></a>

**Topics**
+ [Using a COPY Command to Load Data](t_Loading_tables_with_the_COPY_command.md)
+ [Updating Tables with DML Commands](t_Updating_tables_with_DML_commands.md)
+ [Updating and Inserting New Data](t_updating-inserting-using-staging-tables-.md)
+ [Performing a Deep Copy](performing-a-deep-copy.md)
+ [Analyzing Tables](t_Analyzing_tables.md)
+ [Vacuuming Tables](t_Reclaiming_storage_space202.md)
+ [Managing Concurrent Write Operations](c_Concurrent_writes.md)

A COPY command is the most efficient way to load a table\. You can also add data to your tables using INSERT commands, though it is much less efficient than using COPY\. The COPY command is able to read from multiple data files or multiple data streams simultaneously\. Amazon Redshift allocates the workload to the cluster nodes and performs the load operations in parallel, including sorting the rows and distributing data across node slices\.

**Note**  
Amazon Redshift Spectrum external tables are read\-only\. You can't COPY or INSERT to an external table\.

To access data on other AWS resources, your cluster must have permission to access those resources and to perform the necessary actions to access the data\. You can use Identity and Access Management \(IAM\) to limit the access users have to your cluster resources and data\.

After your initial data load, if you add, modify, or delete a significant amount of data, you should follow up by running a VACUUM command to reorganize your data and reclaim space after deletes\. You should also run an ANALYZE command to update table statistics\.

This section explains how to load data and troubleshoot data loads and presents best practices for loading data\.