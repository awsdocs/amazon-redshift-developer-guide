# Usage Notes<a name="r_CREATE_TABLE_usage"></a>

## Limits<a name="r_CREATE_TABLE_usage-limits"></a>

The maximum number of tables is 9,900 for large and xlarge cluster node types and 20,000 for 8xlarge cluster node types\. The limit includes temporary tables\. Temporary tables include user\-defined temporary tables and temporary tables created by Amazon Redshift during query processing or system maintenance\. Views are not included in this limit\. For more information about cluster node types, see [Clusters and Nodes](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-about-clusters-and-nodes) in the *Amazon Redshift Cluster Management Guide*\. 

The maximum number of characters for a table name is 127\.

The maximum number of columns you can define in a single table is 1,600\. 

The maximum number of SORTKEY columns you can define in a single table is 400\.

## Summary of Column\-Level Settings and Table\-Level Settings<a name="r_CREATE_TABLE_usage-summary_of_settings"></a>

 Several attributes and settings can be set at the column level or at the table level\. In some cases, setting an attribute or constraint at the column level or at the table level has the same effect\. In other cases, they produce different results\. 

 The following list summarizes column\-level and table\-level settings: 

DISTKEY  
There is no difference in effect whether set at the column level or at the table level\.   
If DISTKEY is set, either at the column level or at the table level, DISTSTYLE must be set to KEY or not set at all\. DISTSTYLE can be set only at the table level\. 

SORTKEY  
If set at the column level, SORTKEY must be a single column\. If SORTKEY is set at the table level, one or more columns can make up a compound or interleaved composite sort key\. 

UNIQUE  
At the column level, one or more keys can be set to UNIQUE; the UNIQUE constraint applies to each column individually\. If UNIQUE is set at the table level, one or more columns can make up a composite UNIQUE constraint\. 

PRIMARY KEY  
If set at the column level, PRIMARY KEY must be a single column\. If PRIMARY KEY is set at the table level, one or more columns can make up a composite primary key \. 

FOREIGN KEY  
There is no difference in effect whether FOREIGN KEY is set at the column level or at the table level\. At the column level, the syntax is simply `REFERENCES` *reftable* \[ \( *refcolumn* \)\]\. 

## Distribution of Incoming Data<a name="r_CREATE_TABLE_usage-distribution-of-incoming-data"></a>

When the hash distribution scheme of the incoming data matches that of the target table, no physical distribution of the data is actually necessary when the data is loaded\. For example, if a distribution key is set for the new table and the data is being inserted from another table that is distributed on the same key column, the data is loaded in place, using the same nodes and slices\. However, if the source and target tables are both set to EVEN distribution, data is redistributed into the target table\.

## Wide Tables<a name="r_CREATE_TABLE_usage-wide-tables"></a>

You might be able to create a very wide table but be unable to perform query processing, such as INSERT or SELECT statements, on the table\. The maximum width of a table with fixed width columns, such as CHAR, is 64KB \- 1 \(or 65535 bytes\)\. If at table includes VARCHAR columns, the table can have a larger declared width without returning an error because VARCHARS columns do not contribute their full declared width to the calculated query\-processing limit\. The effective query\-processing limit with VARCHAR columns will vary based on a number of factors\.

If a table is too wide for inserting or selecting, you will receive the following error\.

```
ERROR:  8001
DETAIL:  The combined length of columns processed in the SQL statement
exceeded the query-processing limit of 65535 characters (pid:7627)
```