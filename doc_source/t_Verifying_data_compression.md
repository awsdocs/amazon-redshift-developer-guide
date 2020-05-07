# Testing compression encodings<a name="t_Verifying_data_compression"></a>

If you decide to manually specify column encodings, you might want to test different encodings with your data\.

**Note**  
We recommend that you use the COPY command to load data whenever possible, and allow the COPY command to choose the optimal encodings based on your data\. Alternatively, you can use the [ANALYZE COMPRESSION](r_ANALYZE_COMPRESSION.md) command to view the suggested encodings for existing data\. For details about applying automatic compression, see [Loading tables with automatic compression](c_Loading_tables_auto_compress.md)\.

To perform a meaningful test of data compression, you need a large number of rows\. For this example, we will create a table and insert rows by using a statement that selects from two tables; VENUE and LISTING\. We will leave out the WHERE clause that would normally join the two tables; the result is that *each* row in the VENUE table is joined to *all* of the rows in the LISTING table, for a total of over 32 million rows\. This is known as a Cartesian join and normally is not recommended, but for this purpose, it is a convenient method of creating a lot of rows\. If you have an existing table with data that you want to test, you can skip this step\.

After we have a table with sample data, we create a table with seven columns, each with a different compression encoding: raw, bytedict, lzo, runlength, text255, text32k, and zstd\. We populate each column with exactly the same data by executing an INSERT command that selects the data from the first table\.

To test compression encodings:

1.  \(Optional\) First, we'll use a Cartesian join to create a table with a large number of rows\. Skip this step if you want to test an existing table\. 

   ```
   create table cartesian_venue(
   venueid smallint not null distkey sortkey,
   venuename varchar(100),
   venuecity varchar(30),
   venuestate char(2),
   venueseats integer);
   
   insert into cartesian_venue
   select venueid, venuename, venuecity, venuestate, venueseats
   from venue, listing;
   ```

1.  Next, create a table with the encodings that you want to compare\.  

   ```
   create table encodingvenue (
   venueraw varchar(100) encode raw,
   venuebytedict varchar(100) encode bytedict,
   venuelzo varchar(100) encode lzo,
   venuerunlength varchar(100) encode runlength,
   venuetext255 varchar(100) encode text255,
   venuetext32k varchar(100) encode text32k,
   venuezstd varchar(100) encode zstd);
   ```

1.  Insert the same data into all of the columns using an INSERT statement with a SELECT clause\. 

   ```
   insert into encodingvenue
   select venuename as venueraw, venuename as venuebytedict, venuename as venuelzo, venuename as venuerunlength, venuename as  venuetext32k, venuename as  venuetext255, venuename as venuezstd
   from cartesian_venue;
   ```

1.  Verify the number of rows in the new table\. 

   ```
   select count(*) from encodingvenue
   
     count
   ----------
    38884394
   (1 row)
   ```

1.  Query the [STV\_BLOCKLIST](r_STV_BLOCKLIST.md) system table to compare the number of 1 MB disk blocks used by each column\.  

   The MAX aggregate function returns the highest block number for each column\. The STV\_BLOCKLIST table includes details for three system\-generated columns\. This example uses `col < 6` in the WHERE clause to exclude the system\-generated columns\. 

   ```
   select col, max(blocknum)
   from stv_blocklist b, stv_tbl_perm p
   where (b.tbl=p.id) and name ='encodingvenue'
   and col < 7
   group by name, col
   order by col;
   ```

   The query returns the following results\. The columns are numbered beginning with zero\. Depending on how your cluster is configured, your result might have different numbers, but the relative sizes should be similar\. You can see that BYTEDICT encoding on the second column produced the best results for this dataset, with a compression ratio of better than 20:1\. LZO and ZSTD encoding also produced excellent results\. Different data sets will produce different results, of course\. When a column contains longer text strings, LZO often produces the best compression results\.

   ```
    col | max
   -----+-----
      0 | 203
      1 |  10
      2 |  22
      3 | 204
      4 |  56
      5 |  72
      6 |  20
   (7 rows)
   ```

If you have data in an existing table, you can use the [ANALYZE COMPRESSION](r_ANALYZE_COMPRESSION.md) command to view the suggested encodings for the table\. For example, the following example shows the recommended encoding for a copy of the VENUE table, CARTESIAN\_VENUE, that contains 38 million rows\. Notice that ANALYZE COMPRESSION recommends LZO encoding for the VENUENAME column\. ANALYZE COMPRESSION chooses optimal compression based on multiple factors, which include percent of reduction\. In this specific case, BYTEDICT provides better compression, but LZO also produces greater than 90 percent compression\. 

```
analyze compression cartesian_venue;

Table          | Column     | Encoding | Est_reduction_pct
---------------+------------+----------+------------------
reallybigvenue | venueid    | lzo      | 97.54            
reallybigvenue | venuename  | lzo      | 91.71            
reallybigvenue | venuecity  | lzo      | 96.01            
reallybigvenue | venuestate | lzo      | 97.68            
reallybigvenue | venueseats | lzo      | 98.21
```