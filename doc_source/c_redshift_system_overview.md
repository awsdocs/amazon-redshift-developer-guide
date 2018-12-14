# Amazon Redshift System Overview<a name="c_redshift_system_overview"></a>

**Topics**
+ [Data Warehouse System Architecture](c_high_level_system_architecture.md)
+ [Performance](c_challenges_achieving_high_performance_queries.md)
+ [Columnar Storage](c_columnar_storage_disk_mem_mgmnt.md)
+ [Internal Architecture and System Operation](c_internal_arch_system_operation.md)
+ [Workload Management](c_workload_mngmt_classification.md)
+ [Using Amazon Redshift with Other Services](using-redshift-with-other-services.md)

An Amazon Redshift data warehouse is an enterprise\-class relational database query and management system\.

Amazon Redshift supports client connections with many types of applications, including business intelligence \(BI\), reporting, data, and analytics tools\.

When you execute analytic queries, you are retrieving, comparing, and evaluating large amounts of data in multiple\-stage operations to produce a final result\.

Amazon Redshift achieves efficient storage and optimum query performance through a combination of massively parallel processing, columnar data storage, and very efficient, targeted data compression encoding schemes\. This section presents an introduction to the Amazon Redshift system architecture\.