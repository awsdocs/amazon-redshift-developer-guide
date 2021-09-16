# Working with automatic table optimization<a name="t_Creating_tables"></a>

Automatic table optimization is a self\-tuning capability that automatically optimizes the design of tables by applying sort and distribution keys without the need for administrator intervention\. By using automation to tune the design of tables, you can get started more easily and get the fastest performance quickly without needing to invest time to manually tune and implement table optimizations\. 

Automatic table optimization continuously observes how queries interact with tables\. It uses advanced artificial intelligence methods to choose sort and distribution keys to optimize performance for the cluster's workload\. If Amazon Redshift determines that applying a key improves cluster performance, tables are automatically altered within hours from the time the cluster was created, with minimal impact to queries\. 

To take advantage of this automation, an Amazon Redshift administrator creates a new table, or alters an existing table to enable it to use automatic optimization\. Existing tables with a distribution style or sort key of `AUTO` are already enabled for automation\. When you run queries against those tables, Amazon Redshift determines if a sort key or distribution key will improve performance\. If so, then Amazon Redshift automatically modifies the table without requiring administrator intervention\. If a minimum number of queries are run, optimizations are applied within hours of the cluster being launched\. 

 If Amazon Redshift determines that a distribution key improves the performance of queries, tables where distribution style is `AUTO` can have their distribution style changed to `KEY`\.

**Topics**
+ [Enabling automatic table optimization](#ato-enabling)
+ [Removing automatic table optimization from a table](#ato-disabling)
+ [Monitoring actions of automatic table optimization](#ato-monitoring-actions)
+ [Working with column compression](t_Compressing_data_on_disk.md)
+ [Working with data distribution styles](t_Distributing_data.md)
+ [Working with sort keys](t_Sorting_data.md)
+ [Defining table constraints](t_Defining_constraints.md)

## Enabling automatic table optimization<a name="ato-enabling"></a>

By default, tables created without explicitly defining sort keys or distributions keys are set to `AUTO`\. At the time of table creation, you can also explicitly set a sort or a distribution key manually\. If you set the sort or distribution key, then the table is not automatically managed\. 

To enable an existing table to be automatically optimized, use the ALTER statement options to change the table to `AUTO`\. You might choose to define automation for sort keys, but not for distribution keys \(and vice versa\)\.  If you run an ALTER statement to convert a table to be an automated table, existing sort keys and distribution styles are preserved\. 

```
ALTER TABLE table_name ALTER SORTKEY AUTO;
```

```
ALTER TABLE table_name ALTER DISTSTYLE AUTO;
```

For more information, see [ALTER TABLE](r_ALTER_TABLE.md)\.

Initially, a table has no distribution key or sort key\. The distribution style is set to either `EVEN` or `ALL` depending on table size\. As the table grows in size, Amazon Redshift applies the optimal distribution keys and sort keys\. Optimizations are applied within hours after a minimum number of queries are run\. When determining sort key optimizations, Amazon Redshift attempts to optimize the data blocks read from disk during a table scan\. When determining distribution style optimizations, Amazon Redshift tries to optimize the number of bytes transferred between cluster nodes\. 

## Removing automatic table optimization from a table<a name="ato-disabling"></a>

You can remove a table from automatic optimization\. Removing a table from automation involves selecting a sort key or distribution style\. To change distribution style, specify a specific distribution style\. 

```
ALTER TABLE table_name ALTER DISTSTYLE EVEN;
```

```
ALTER TABLE table_name ALTER DISTSTYLE ALL;
```

```
ALTER TABLE table_name ALTER DISTSTYLE KEY DISTKEY c1;
```

To change a sort key, you can define a sort key or choose none\. 

```
ALTER TABLE table_name ALTER SORTKEY(c1, c2);
```

```
ALTER TABLE table_name ALTER SORTKEY NONE;
```

## Monitoring actions of automatic table optimization<a name="ato-monitoring-actions"></a>

The system view `SVV_ALTER_TABLE_RECOMMENDATIONS` records the current Amazon Redshift Advisor recommendations for tables\. This view shows recommendations for all tables, those that are defined for automatic optimization and those that aren't\. 

To view if a table is defined for automatic optimization, query the system view `SVV_TABLE_INFO`\. Entries appear only for tables visible in the current session's database\. Recommendations are inserted into the view twice per day starting within hours from the time the cluster was created\. After a recommendation is available, it's started within an hour\. After a recommendation has been applied \(either by Amazon Redshift or by you\), it no longer appears in the view\. 

The system view `SVL_AUTO_WORKER_ACTION` shows an audit log of all the actions taken by the Amazon Redshift, and the previous state of the table\.

The system view `SVV_TABLE_INFO` lists all of the tables in the system, along with a column to indicate whether the sort key and distribution style of the table is set to `AUTO`\. 

For more information about these system views, see [System views](c_intro_system_views.md)\.