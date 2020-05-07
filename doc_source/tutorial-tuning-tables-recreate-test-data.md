# Step 6: Recreate the test data set<a name="tutorial-tuning-tables-recreate-test-data"></a>

Now that you have chosen the sort keys and distribution styles for each of the tables, you can create the tables using those attributes and reload the data\. You will allow the COPY command to analyze the load data and apply compression encodings automatically\.

## To recreate the test data set<a name="tutorial-tuning-tables-to-recreate-test-data"></a>

1. You need to drop the SSB tables before you run the CREATE TABLE commands\. 

   Execute the following commands\. 

   ```
   drop table part cascade;
   drop table supplier cascade;
   drop table customer cascade;
   drop table dwdate cascade;
   drop table lineorder cascade;
   ```

1. Create the tables with sort keys and distribution styles\. 

   Execute the following set of SQL CREATE TABLE commands\.

   ```
   CREATE TABLE part (
     p_partkey     	integer     	not null	sortkey distkey,
     p_name        	varchar(22) 	not null,
     p_mfgr        	varchar(6)      not null,
     p_category    	varchar(7)      not null,
     p_brand1      	varchar(9)      not null,
     p_color       	varchar(11) 	not null,
     p_type        	varchar(25) 	not null,
     p_size        	integer     	not null,
     p_container   	varchar(10)     not null
   );
   
   CREATE TABLE supplier (
     s_suppkey     	integer        not null sortkey,
     s_name        	varchar(25)    not null,
     s_address     	varchar(25)    not null,
     s_city        	varchar(10)    not null,
     s_nation      	varchar(15)    not null,
     s_region      	varchar(12)    not null,
     s_phone       	varchar(15)    not null)
   diststyle all;
   
   CREATE TABLE customer (
     c_custkey     	integer        not null sortkey,
     c_name        	varchar(25)    not null,
     c_address     	varchar(25)    not null,
     c_city        	varchar(10)    not null,
     c_nation      	varchar(15)    not null,
     c_region      	varchar(12)    not null,
     c_phone       	varchar(15)    not null,
     c_mktsegment      varchar(10)    not null)
   diststyle all;
   
   CREATE TABLE dwdate (
     d_datekey            integer       not null sortkey,
     d_date               varchar(19)   not null,
     d_dayofweek	      varchar(10)   not null,
     d_month      	    varchar(10)   not null,
     d_year               integer       not null,
     d_yearmonthnum       integer  	 not null,
     d_yearmonth          varchar(8)	not null,
     d_daynuminweek       integer       not null,
     d_daynuminmonth      integer       not null,
     d_daynuminyear       integer       not null,
     d_monthnuminyear     integer       not null,
     d_weeknuminyear      integer       not null,
     d_sellingseason      varchar(13)    not null,
     d_lastdayinweekfl    varchar(1)    not null,
     d_lastdayinmonthfl   varchar(1)    not null,
     d_holidayfl          varchar(1)    not null,
     d_weekdayfl          varchar(1)    not null)
   diststyle all;
   
   CREATE TABLE lineorder (
     lo_orderkey      	    integer     	not null,
     lo_linenumber        	integer     	not null,
     lo_custkey           	integer     	not null,
     lo_partkey           	integer     	not null distkey,
     lo_suppkey           	integer     	not null,
     lo_orderdate         	integer     	not null sortkey,
     lo_orderpriority     	varchar(15)     not null,
     lo_shippriority      	varchar(1)      not null,
     lo_quantity          	integer     	not null,
     lo_extendedprice     	integer     	not null,
     lo_ordertotalprice   	integer     	not null,
     lo_discount          	integer     	not null,
     lo_revenue           	integer     	not null,
     lo_supplycost        	integer     	not null,
     lo_tax               	integer     	not null,
     lo_commitdate         integer         not null,
     lo_shipmode          	varchar(10)     not null
   );
   ```

1. Load the tables using the same sample data\. 

   1. Open the `loadssb.sql` script that you created in the first step\. 

   1. Delete `compupdate off` from each COPY statement\. This time, you will allow COPY to apply compression encodings\. 

      For reference, the edited script should look like the following: 

      ```
      copy customer from 's3://awssampledbuswest2/ssbgz/customer' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
      gzip region 'us-west-2';
      
      copy dwdate from 's3://awssampledbuswest2/ssbgz/dwdate' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>' 
      gzip region 'us-west-2';
      
      copy lineorder from 's3://awssampledbuswest2/ssbgz/lineorder' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>'
      gzip region 'us-west-2';
      
      copy part from 's3://awssampledbuswest2/ssbgz/part' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>'
      gzip region 'us-west-2';
      
      copy supplier from 's3://awssampledbuswest2/ssbgz/supplier' 
      credentials 'aws_access_key_id=<Your-Access-Key-ID>;aws_secret_access_key=<Your-Secret-Access-Key>'
      gzip region 'us-west-2';
      ```

   1. Save the file\. 

   1. Execute the COPY commands either by running the SQL script or by copying and pasting the commands into your SQL client\. 
**Note**  
The load operation will take about 10 to 15 minutes\. This might be a good time to get another cup of tea or feed the fish\. 

      Your results should look similar to the following\. 

      ```
      Warnings:
      Load into table 'customer' completed, 3000000 record(s) loaded successfully.
      ...
      ...
      Script execution finished
      Total script execution time: 12m 15s
      ```

   1. Record the load time in the benchmarks table\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables-recreate-test-data.html)

## Next step<a name="next-step-retest"></a>

[Step 7: Retest system performance after tuning](tutorial-tuning-tables-retest.md)