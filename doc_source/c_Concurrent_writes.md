# Managing concurrent write operations<a name="c_Concurrent_writes"></a>

**Topics**
+ [Serializable isolation](c_serial_isolation.md)
+ [Write and read\-write operations](c_write_readwrite.md)
+ [Concurrent write examples](r_Serializable_isolation_example.md)

Amazon Redshift allows tables to be read while they are being incrementally loaded or modified\.

In some traditional data warehousing and business intelligence applications, the database is available to users only when the nightly load is complete\. In such cases, no updates are allowed during regular work hours, when analytic queries are run and reports are generated; however, an increasing number of applications remain live for long periods of the day or even all day, making the notion of a load window obsolete\.

Amazon Redshift supports these types of applications by allowing tables to be read while they are being incrementally loaded or modified\. Queries simply see the latest committed version, or *snapshot*, of the data, rather than waiting for the next version to be committed\. If you want a particular query to wait for a commit from another write operation, you have to schedule it accordingly\.

The following topics describe some of the key concepts and use cases that involve transactions, database snapshots, updates, and concurrent behavior\.