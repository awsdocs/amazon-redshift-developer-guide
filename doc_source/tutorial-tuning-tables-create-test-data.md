# Step 1: Create a test data set<a name="tutorial-tuning-tables-create-test-data"></a>

Data warehouse databases commonly use a star schema design, in which a central fact table contains the core data for the database and several dimension tables provide descriptive attribute information for the fact table\. The fact table joins each dimension table on a foreign key that matches the dimension's primary key\. 

**Star Schema Benchmark \(SSB\) **

For this tutorial, you will use a set of five tables based on the Star Schema Benchmark \(SSB\) schema\. The following diagram shows the SSB data model\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/tutorial-optimize-tables-ssb-data-model.png)

## To create a test data set<a name="tutorial-tuning-tables-to-create-test-data"></a>

You will create a set of tables without sort keys, distribution styles, or compression encodings\. Then you will load the tables with data from the SSB data set\.

1. \(Optional\) Launch a cluster\.

   If you already have a cluster that you want to use, you can skip this step\. Your cluster should have at least two nodes\. For the exercises in this tutorial, you will use a four\-node cluster\.

   To launch a dc2\.large cluster with four nodes, follow the steps in [Amazon Redshift Getting Started](https://docs.aws.amazon.com/redshift/latest/gsg/), but select **Multi Node** for **Cluster Type** and set **Number of Compute Nodes** to **4**\.

   Follow the steps to connect to your cluster from a SQL client and test a connection\. You do not need to complete the remaining steps to create tables, upload data, and try example queries\. 

1. Create the SSB test tables using minimum attributes\.
**Note**  
If the SSB tables already exist in the current database, you will need to drop the tables first\. See [Step 6: Recreate the test data set](tutorial-tuning-tables-recreate-test-data.md) for the DROP TABLE commands\.

   For the purposes of this tutorial, the first time you create the tables, they will not have sort keys, distribution styles, or compression encodings\. 

   Execute the following CREATE TABLE commands\. 

   ```
   CREATE TABLE part 
   (
     p_partkey     INTEGER NOT NULL,
     p_name        VARCHAR(22) NOT NULL,
     p_mfgr        VARCHAR(6) NOT NULL,
     p_category    VARCHAR(7) NOT NULL,
     p_brand1      VARCHAR(9) NOT NULL,
     p_color       VARCHAR(11) NOT NULL,
     p_type        VARCHAR(25) NOT NULL,
     p_size        INTEGER NOT NULL,
     p_container   VARCHAR(10) NOT NULL
   );
   
   CREATE TABLE supplier 
   (
     s_suppkey   INTEGER NOT NULL,
     s_name      VARCHAR(25) NOT NULL,
     s_address   VARCHAR(25) NOT NULL,
     s_city      VARCHAR(10) NOT NULL,
     s_nation    VARCHAR(15) NOT NULL,
     s_region    VARCHAR(12) NOT NULL,
     s_phone     VARCHAR(15) NOT NULL
   );
   
   CREATE TABLE customer 
   (
     c_custkey      INTEGER NOT NULL,
     c_name         VARCHAR(25) NOT NULL,
     c_address      VARCHAR(25) NOT NULL,
     c_city         VARCHAR(10) NOT NULL,
     c_nation       VARCHAR(15) NOT NULL,
     c_region       VARCHAR(12) NOT NULL,
     c_phone        VARCHAR(15) NOT NULL,
     c_mktsegment   VARCHAR(10) NOT NULL
   );
   
   CREATE TABLE dwdate 
   (
     d_datekey            INTEGER NOT NULL,
     d_date               VARCHAR(19) NOT NULL,
     d_dayofweek          VARCHAR(10) NOT NULL,
     d_month              VARCHAR(10) NOT NULL,
     d_year               INTEGER NOT NULL,
     d_yearmonthnum       INTEGER NOT NULL,
     d_yearmonth          VARCHAR(8) NOT NULL,
     d_daynuminweek       INTEGER NOT NULL,
     d_daynuminmonth      INTEGER NOT NULL,
     d_daynuminyear       INTEGER NOT NULL,
     d_monthnuminyear     INTEGER NOT NULL,
     d_weeknuminyear      INTEGER NOT NULL,
     d_sellingseason      VARCHAR(13) NOT NULL,
     d_lastdayinweekfl    VARCHAR(1) NOT NULL,
     d_lastdayinmonthfl   VARCHAR(1) NOT NULL,
     d_holidayfl          VARCHAR(1) NOT NULL,
     d_weekdayfl          VARCHAR(1) NOT NULL
   );
   CREATE TABLE lineorder 
   (
     lo_orderkey          INTEGER NOT NULL,
     lo_linenumber        INTEGER NOT NULL,
     lo_custkey           INTEGER NOT NULL,
     lo_partkey           INTEGER NOT NULL,
     lo_suppkey           INTEGER NOT NULL,
     lo_orderdate         INTEGER NOT NULL,
     lo_orderpriority     VARCHAR(15) NOT NULL,
     lo_shippriority      VARCHAR(1) NOT NULL,
     lo_quantity          INTEGER NOT NULL,
     lo_extendedprice     INTEGER NOT NULL,
     lo_ordertotalprice   INTEGER NOT NULL,
     lo_discount          INTEGER NOT NULL,
     lo_revenue           INTEGER NOT NULL,
     lo_supplycost        INTEGER NOT NULL,
     lo_tax               INTEGER NOT NULL,
     lo_commitdate        INTEGER NOT NULL,
     lo_shipmode          VARCHAR(10) NOT NULL
   );
   ```

1. Load the tables using SSB sample data\.

   The sample data for this tutorial is provided in an Amazon S3 buckets that give read access to all authenticated AWS users, so any valid AWS credentials that permit access to Amazon S3 will work\. 

   1. Create a new text file named `loadssb.sql` containing the following SQL\.

      ```
      copy customer from 's3://awssampledbuswest2/ssbgz/customer' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
      gzip compupdate off region 'us-west-2';
      
      copy dwdate from 's3://awssampledbuswest2/ssbgz/dwdate' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
      gzip compupdate off region 'us-west-2';
      
      copy lineorder from 's3://awssampledbuswest2/ssbgz/lineorder' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>'
      gzip compupdate off region 'us-west-2';
      
      copy part from 's3://awssampledbuswest2/ssbgz/part' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>'
      gzip compupdate off region 'us-west-2';
      
      copy supplier from 's3://awssampledbuswest2/ssbgz/supplier' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>'
      gzip compupdate off region 'us-west-2';
      ```

   1. Replace *<Your\-Access\-Key\-ID>* and *<Your\-Secret\-Access\-Key>* with your own AWS account credentials\. The segment of the credentials string that is enclosed in single quotation marks must not contain any spaces or line breaks\. 

   1. Execute the COPY commands either by running the SQL script or by copying and pasting the commands into your SQL client\. 
**Note**  
The load operation will take about 10 to 15 minutes for all five tables\. 

      Your results should look similar to the following\. 

      ```
      Load into table 'customer' completed, 3000000 record(s) loaded successfully.
      
      0 row(s) affected.
      copy executed successfully
      
      Execution time: 10.28s
      (Statement 1 of 5 finished)
      ...
      ...
      Script execution finished
      Total script execution time: 9m 51s
      ```

1. Sum the execution time for all five tables, or else note the total script execution time\. You’ll record that number as the load time in the benchmarks table in Step 2, following\. 

1. To verify that each table loaded correctly, execute the following commands\. 

   ```
   select count(*) from LINEORDER;
   select count(*) from PART;
   select count(*) from  CUSTOMER;
   select count(*) from  SUPPLIER;
   select count(*) from  DWDATE;
   ```

   The following results table shows the number of rows for each SSB table\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables-create-test-data.html)

## Next step<a name="next-step-test-performance"></a>

[Step 2: Test system performance to establish a baseline](tutorial-tuning-tables-test-performance.md)