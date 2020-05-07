# Improving Amazon Redshift Spectrum query performance<a name="c-spectrum-external-performance"></a>

Look at the query plan to find what steps have been pushed to the Amazon Redshift Spectrum layer\. 

The following steps are related to the Redshift Spectrum query:
+ S3 Seq Scan 
+ S3 HashAggregate 
+ S3 Query Scan
+ Seq Scan PartitionInfo
+ Partition Loop 

The following example shows the query plan for a query that joins an external table with a local table\. Note the S3 Seq Scan and S3 HashAggregate steps that were executed against the data on Amazon S3\.

```
explain
select top 10 spectrum.sales.eventid, sum(spectrum.sales.pricepaid) 
from spectrum.sales, event
where spectrum.sales.eventid = event.eventid
and spectrum.sales.pricepaid > 30
group by spectrum.sales.eventid
order by 2 desc;
```

```
QUERY PLAN                                                                                                                                                                                
-----------------------------------------------------------------------------
XN Limit  (cost=1001055770628.63..1001055770628.65 rows=10 width=31)                                                                                                                      
  ->  XN Merge  (cost=1001055770628.63..1001055770629.13 rows=200 width=31)                                                                                                               
        Merge Key: sum(sales.derived_col2)                                                                                                                                                
        ->  XN Network  (cost=1001055770628.63..1001055770629.13 rows=200 width=31)                                                                                                       
              Send to leader                                                                                                                                                              
              ->  XN Sort  (cost=1001055770628.63..1001055770629.13 rows=200 width=31)                                                                                                    
                    Sort Key: sum(sales.derived_col2)                                                                                                                                     
                    ->  XN HashAggregate  (cost=1055770620.49..1055770620.99 rows=200 width=31)                                                                                           
                          ->  XN Hash Join DS_BCAST_INNER  (cost=3119.97..1055769620.49 rows=200000 width=31)                                                                             
                                Hash Cond: ("outer".derived_col1 = "inner".eventid)                                                                                                       
                                ->  XN S3 Query Scan sales  (cost=3010.00..5010.50 rows=200000 width=31)                                                                                  
                                      ->  S3 HashAggregate  (cost=3010.00..3010.50 rows=200000 width=16)                                                                                  
                                            ->  S3 Seq Scan spectrum.sales location:"s3://awssampledbuswest2/tickit/spectrum/sales" format:TEXT  (cost=0.00..2150.00 rows=172000 width=16)
                                                  Filter: (pricepaid > 30.00)                                                                                                             
                                ->  XN Hash  (cost=87.98..87.98 rows=8798 width=4)                                                                                                        
                                      ->  XN Seq Scan on event  (cost=0.00..87.98 rows=8798 width=4)
```

Note the following elements in the query plan:
+ The `S3 Seq Scan` node shows the filter `pricepaid > 30.00` was processed in the Redshift Spectrum layer\.

  A filter node under the `XN S3 Query Scan` node indicates predicate processing in Amazon Redshift on top of the data returned from the Redshift Spectrum layer\.
+ The `S3 HashAggregate` node indicates aggregation in the Redshift Spectrum layer for the group by clause \(`group by spectrum.sales.eventid`\)\.

Following are ways to improve Redshift Spectrum performance:
+ Use Apache Parquet formatted data files\. Parquet stores data in a columnar format, so Redshift Spectrum can eliminate unneeded columns from the scan\. When data is in text\-file format, Redshift Spectrum needs to scan the entire file\.
+ Use the fewest columns possible in your queries\.
+ Use multiple files to optimize for parallel processing\. Keep your file sizes larger than 64 MB\. Avoid data size skew by keeping files about the same size\.
+ Put your large fact tables in Amazon S3 and keep your frequently used, smaller dimension tables in your local Amazon Redshift database\.
+ Update external table statistics by setting the TABLE PROPERTIES numRows parameter\. Use [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md) or [ALTER TABLE](r_ALTER_TABLE.md) to set the TABLE PROPERTIES numRows parameter to reflect the number of rows in the table\. Amazon Redshift doesn't analyze external tables to generate the table statistics that the query optimizer uses to generate a query plan\. If table statistics aren't set for an external table, Amazon Redshift generates a query execution plan\. Amazon Redshift generates this plan based on the assumption that external tables are the larger tables and local tables are the smaller tables\.
+ The Amazon Redshift query planner pushes predicates and aggregations to the Redshift Spectrum query layer whenever possible\. When large amounts of data are returned from Amazon S3, the processing is limited by your cluster's resources\. Redshift Spectrum scales automatically to process large requests\. Thus, your overall performance improves whenever you can push processing to the Redshift Spectrum layer\. 
+ Write your queries to use filters and aggregations that are eligible to be pushed to the Redshift Spectrum layer\.

  The following are examples of some operations that can be pushed to the Redshift Spectrum layer:
  + GROUP BY clauses
  + Comparison conditions and pattern\-matching conditions, such as LIKE\.
  + Aggregate functions, such as COUNT, SUM, AVG, MIN, and MAX\.
  + String functions\.

  Operations that can't be pushed to the Redshift Spectrum layer include DISTINCT and ORDER BY\.
+ Use partitions to limit the data that is scanned\. Partition your data based on your most common query predicates, then prune partitions by filtering on partition columns\. For more information, see [Partitioning Redshift Spectrum external tables](c-spectrum-external-tables.md#c-spectrum-external-tables-partitioning)\.

  Query [SVL\_S3PARTITION](r_SVL_S3PARTITION.md) to view total partitions and qualified partitions\.