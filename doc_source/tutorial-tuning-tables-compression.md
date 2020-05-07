# Step 5: Review compression encodings<a name="tutorial-tuning-tables-compression"></a>

Compression is a column\-level operation that reduces the size of data when it is stored\. Compression conserves storage space and reduces the size of data that is read from storage, which reduces the amount of disk I/O and therefore improves query performance\. 

By default, Amazon Redshift stores data in its raw, uncompressed format\. When you create tables in an Amazon Redshift database, you can define a compression type, or encoding, for the columns\. For more information, see [Compression encodings](c_Compression_encodings.md)\. 

You can apply compression encodings to columns in tables manually when you create the tables, or you can use the COPY command to analyze the load data and apply compression encodings automatically\. 

## To review compression encodings<a name="tutorial-tuning-tables-compression-review-encodings"></a>

1. Find how much space each column uses\.

   Query the STV\_BLOCKLIST system view to find the number of 1 MB blocks each column uses\. The MAX aggregate function returns the highest block number for each column\. This example uses `col < 17` in the WHERE clause to exclude system\-generated columns\. 

   Execute the following command\. 

   ```
   select col, max(blocknum)
   from stv_blocklist b, stv_tbl_perm p
   where (b.tbl=p.id) and name ='lineorder'
   and col < 17
   group by name, col
   order by col;
   ```

   Your results will look similar to the following\.

   ```
   col | max 
   ----+-----
     0 |  572
     1 |  572
     2 |  572
     3 |  572
     4 |  572
     5 |  572
     6 | 1659
     7 |  715
     8 |  572
     9 |  572
    10 |  572
    11 |  572
    12 |  572
    13 |  572
    14 |  572
    15 |  572
    16 | 1185
   (17 rows)
   ```

1. Experiment with the different encoding methods\.

   In this step, you create a table with identical columns, except that each column uses a different compression encoding\. Then you insert a large number of rows, using data from the `p_name` column in the PART table, so that every column has the same data\. Finally, you will examine the table to compare the effects of the different encodings on column sizes\.

   1. Create a table with the encodings that you want to compare\. 

      ```
      create table encodingshipmode (
      moderaw varchar(22) encode raw,
      modebytedict varchar(22) encode bytedict,
      modelzo varchar(22) encode lzo,
      moderunlength varchar(22) encode runlength,
      modetext255 varchar(22) encode text255,
      modetext32k varchar(22) encode text32k);
      ```

   1. Insert the same data into all of the columns using an INSERT statement with a SELECT clause\. The command will take a couple minutes to execute\.

      ```
      insert into encodingshipmode
      select lo_shipmode as moderaw, lo_shipmode as modebytedict, lo_shipmode as modelzo,
      lo_shipmode as moderunlength, lo_shipmode as modetext255,
      lo_shipmode as modetext32k
      from lineorder where lo_orderkey < 200000000;
      ```

   1. Query the STV\_BLOCKLIST system table to compare the number of 1 MB disk blocks used by each column\. 

      ```
      select col, max(blocknum)
      from stv_blocklist b, stv_tbl_perm p
      where (b.tbl=p.id) and name = 'encodingshipmode'
      and col < 6
      group by name, col
      order by col;
      ```

      The query returns results similar to the following\. Depending on how your cluster is configured, your results will be different, but the relative sizes should be similar\. 

      ```
         col | max
      –------+-----
        0    | 221
        1    | 26
        2    | 61
        3    | 192
        4    | 54
        5    | 105
      (6 rows)
      ```

      The columns show the results for the following encodings: 
      + Raw 
      + Bytedict 
      + LZO 
      + Runlength 
      + Text255 
      + Text32K 

      You can see that Bytedict encoding on the second column produced the best results for this data set, with a compression ratio of better than 8:1\. Different data sets will produce different results, of course\. 

1. Use the ANALYZE COMPRESSION command to view the suggested encodings for an existing table\. 

   Execute the following command\. 

   ```
   analyze compression lineorder;
   ```

   Your results should look similar to the following\.

   ```
      Table   | Column	          |    Encoding
   -----------+------------------+-------------------
   lineorder    lo_orderkey           delta
   lineorder    lo_linenumber         delta
   lineorder    lo_custkey            raw
   lineorder    lo_partkey            raw
   lineorder    lo_suppkey            raw
   lineorder    lo_orderdate          delta32k
   lineorder    lo_orderpriority      bytedict
   lineorder    lo_shippriority       runlength
   lineorder    lo_quantity           delta
   lineorder    lo_extendedprice      lzo
   lineorder    lo_ordertotalprice    lzo
   lineorder    lo_discount           delta
   lineorder    lo_revenue            lzo
   lineorder    lo_supplycost         delta32k
   lineorder    lo_tax                delta
   lineorder    lo_commitdate         delta32k
   lineorder    lo_shipmode           bytedict
   ```

   Notice that ANALYZE COMPRESSION chose BYTEDICT encoding for the `lo_shipmode` column\. 

   For an example that walks through choosing manually applied compression encodings, see [Example: Choosing compression encodings for the CUSTOMER table](Examples__compression_encodings_in_CREATE_TABLE_statements.md)\. 

1. Apply automatic compression to the SSB tables\.

   By default, the COPY command automatically applies compression encodings when you load data into an empty table that has no compression encodings other than RAW encoding\. For this tutorial, you will let the COPY command automatically select and apply optimal encodings for the tables as part of the next step, Recreate the test data set\.

   For more information, see [Loading tables with automatic compression](c_Loading_tables_auto_compress.md)\.

## Next step<a name="next-step-recreate-data"></a>

[Step 6: Recreate the test data set](tutorial-tuning-tables-recreate-test-data.md)