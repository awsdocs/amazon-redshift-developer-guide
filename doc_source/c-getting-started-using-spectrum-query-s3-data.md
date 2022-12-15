# Step 4: Query your data in Amazon S3<a name="c-getting-started-using-spectrum-query-s3-data"></a>

After your external tables are created, you can query them using the same SELECT statements that you use to query other Amazon Redshift tables\. These SELECT statement queries include joining tables, aggregating data, and filtering on predicates\. <a name="spectrum-get-started-query-s3-data"></a>

**To query your data in Amazon S3**

1. Get the number of rows in the MYSPECTRUM\_SCHEMA\.SALES table\. 

   ```
   select count(*) from myspectrum_schema.sales;
   ```

   ```
   count 
   ------
   172462
   ```

1. Keep your larger fact tables in Amazon S3 and your smaller dimension tables in Amazon Redshift, as a best practice\. If you loaded the sample data in [Getting Started with Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html), you have a table named EVENT in your database\. If not, create the EVENT table by using the following command\.

   ```
   create table event(
   eventid integer not null distkey,
   venueid smallint not null,
   catid smallint not null,
   dateid smallint not null sortkey,
   eventname varchar(200),
   starttime timestamp);
   ```

1. Load the EVENT table by replacing the IAM role ARN in the following COPY command with the role ARN you created in [Step 1\. Create an IAM role for Amazon Redshift](c-getting-started-using-spectrum-create-role.md)\. You can optionally download and view the [ source data for the `allevents_pipe.txt`](https://s3.amazonaws.com/redshift-downloads/tickit/allevents_pipe.txt) from an Amazon S3 bucket in AWS Region `us-east-1`\.

   ```
   copy event from 's3://redshift-downloads/tickit/allevents_pipe.txt' 
   iam_role 'arn:aws:iam::123456789012:role/myspectrum_role'
   delimiter '|' timeformat 'YYYY-MM-DD HH:MI:SS' region 'us-east-1';
   ```

   The following example joins the external Amazon S3 table MYSPECTRUM\_SCHEMA\.SALES with the local Amazon Redshift table EVENT to find the total sales for the top 10 events\.

   ```
   select top 10 myspectrum_schema.sales.eventid, sum(myspectrum_schema.sales.pricepaid) from myspectrum_schema.sales, event
   where myspectrum_schema.sales.eventid = event.eventid
   and myspectrum_schema.sales.pricepaid > 30
   group by myspectrum_schema.sales.eventid
   order by 2 desc;
   ```

   ```
   eventid | sum     
   --------+---------
       289 | 51846.00
      7895 | 51049.00
      1602 | 50301.00
       851 | 49956.00
      7315 | 49823.00
      6471 | 47997.00
      2118 | 47863.00
       984 | 46780.00
      7851 | 46661.00
      5638 | 46280.00
   ```

1. View the query plan for the previous query\. Notice the `S3 Seq Scan`, `S3 HashAggregate`, and `S3 Query Scan` steps that were run against the data on Amazon S3\.

   ```
   explain
   select top 10 myspectrum_schema.sales.eventid, sum(myspectrum_schema.sales.pricepaid) 
   from myspectrum_schema.sales, event
   where myspectrum_schema.sales.eventid = event.eventid
   and myspectrum_schema.sales.pricepaid > 30
   group by myspectrum_schema.sales.eventid
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
                                               ->  S3 Seq Scan myspectrum_schema.sales location:"s3://redshift-downloads/tickit/spectrum/sales" format:TEXT  (cost=0.00..2150.00 rows=172000 width=16)
                                                     Filter: (pricepaid > 30.00)                                                                                                             
                                   ->  XN Hash  (cost=87.98..87.98 rows=8798 width=4)                                                                                                        
                                         ->  XN Seq Scan on event  (cost=0.00..87.98 rows=8798 width=4)
   ```