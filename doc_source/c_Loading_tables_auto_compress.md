# Loading tables with automatic compression<a name="c_Loading_tables_auto_compress"></a>

**Topics**
+ [How automatic compression works](#c_Loading_tables_auto_compress-how-automatic-compression-works)
+ [Automatic compression example](#r_COPY_COMPRESS_examples)

You can apply compression encodings to columns in tables manually, based on your own evaluation of the data\. Or you can use the COPY command with COMPUPDATE set to ON to analyze and apply compression automatically based on sample data\. 

You can use automatic compression when you create and load a brand new table\. The COPY command performs a compression analysis\. You can also perform a compression analysis without loading data or changing the compression on a table by running the [ANALYZE COMPRESSION](r_ANALYZE_COMPRESSION.md) command on an already populated table\. For example, you can run ANALYZE COMPRESSION when you want to analyze compression on a table for future use, while preserving the existing data definition language \(DDL\) statements\.

Automatic compression balances overall performance when choosing compression encodings\. Range\-restricted scans might perform poorly if sort key columns are compressed much more highly than other columns in the same query\. As a result, automatic compression skips the data analyzing phase on the sort key columns and keeps the user\-defined encoding types\. 

Automatic compression chooses RAW encoding if you haven't explicitly defined a type of encoding\. ANALYZE COMPRESSION behaves the same\. For optimal query performance, consider using RAW for sort keys\.

## How automatic compression works<a name="c_Loading_tables_auto_compress-how-automatic-compression-works"></a>

When the COMPUPDATE parameter is ON, the COPY command applies automatic compression whenever you run the COPY command with an empty target table and all of the table columns either have RAW encoding or no encoding\.

To apply automatic compression to an empty table, regardless of its current compression encodings, run the COPY command with the COMPUPDATE option set to ON\. To disable automatic compression, run the COPY command with the COMPUPDATE option set to OFF\.

You cannot apply automatic compression to a table that already contains data\.

**Note**  
Automatic compression analysis requires enough rows in the load data \(at least 100,000 rows per slice\) to generate a meaningful sample\.

Automatic compression performs these operations in the background as part of the load transaction:

1. An initial sample of rows is loaded from the input file\. Sample size is based on the value of the COMPROWS parameter\. The default is 100,000\.

1. Compression options are chosen for each column\.

1. The sample rows are removed from the table\.

1. The table is recreated with the chosen compression encodings\.

1. The entire input file is loaded and compressed using the new encodings\.

After you run the COPY command, the table is fully loaded, compressed, and ready for use\. If you load more data later, appended rows are compressed according to the existing encoding\.

If you only want to perform a compression analysis, run ANALYZE COMPRESSION, which is more efficient than running a full COPY\. Then you can evaluate the results to decide whether to use automatic compression or recreate the table manually\.

Automatic compression is supported only for the COPY command\. Alternatively, you can manually apply compression encoding when you create the table\. For information about manual compression encoding, see [Choosing a column compression type](t_Compressing_data_on_disk.md)\.

## Automatic compression example<a name="r_COPY_COMPRESS_examples"></a>

In this example, assume that the TICKIT database contains a copy of the LISTING table called BIGLIST, and you want to apply automatic compression to this table when it is loaded with approximately 3 million rows\.

**To load and automatically compress the table**

1. Ensure that the table is empty\. You can apply automatic compression only to an empty table:

   ```
   truncate biglist;
   ```

1. Load the table with a single COPY command\. Although the table is empty, some earlier encoding might have been specified\. To ensure that Amazon Redshift performs a compression analysis, set the COMPUPDATE parameter to ON\.

   ```
   copy biglist from 's3://mybucket/biglist.txt' 
   iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
   delimiter '|' COMPUPDATE ON;
   ```

   Because no COMPROWS option is specified, the default and recommended sample size of 100,000 rows per slice is used\.

1. Look at the new schema for the BIGLIST table in order to review the automatically chosen encoding schemes\.

   ```
   select "column", type, encoding 
   from pg_table_def where tablename = 'biglist';
   
      Column      |            Type               | Encoding 
   ---------------+-----------------------------+----------
   listid         | integer                     | delta    
   sellerid       | integer                     | delta32k 
   eventid        | integer                     | delta32k 
   dateid         | smallint                    | delta    
   +numtickets    | smallint                    | delta    
   priceperticket | numeric(8,2)                | delta32k 
   totalprice     | numeric(8,2)                | mostly32 
   listtime       | timestamp without time zone | none
   ```

1. Verify that the expected number of rows were loaded: 

   ```
   select count(*) from biglist;
   
   count
   ---------
   3079952
   (1 row)
   ```

When rows are later appended to this table using COPY or INSERT statements, the same compression encodings are applied\.