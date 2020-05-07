# Choosing a column compression type<a name="t_Compressing_data_on_disk"></a>

**Topics**
+ [Compression encodings](c_Compression_encodings.md)
+ [Testing compression encodings](t_Verifying_data_compression.md)
+ [Example: Choosing compression encodings for the CUSTOMER table](Examples__compression_encodings_in_CREATE_TABLE_statements.md)

Compression is a column\-level operation that reduces the size of data when it is stored\. Compression conserves storage space and reduces the size of data that is read from storage, which reduces the amount of disk I/O and therefore improves query performance\.

You can apply a compression type, or *encoding*, to the columns in a table manually when you create the table, or you can use the COPY command to analyze and apply compression automatically\. For details about applying automatic compression, see [Loading tables with automatic compression](c_Loading_tables_auto_compress.md)\.

**Note**  
We strongly recommend using the COPY command to apply automatic compression\.

You might choose to apply compression encodings manually if the new table shares the same data characteristics as another table, or if in testing you discover that the compression encodings that are applied during automatic compression are not the best fit for your data\. If you choose to apply compression encodings manually, you can run the [ANALYZE COMPRESSION](r_ANALYZE_COMPRESSION.md) command against an already populated table and use the results to choose compression encodings\.

To apply compression manually, you specify compression encodings for individual columns as part of the CREATE TABLE statement\. The syntax is as follows:

```
CREATE TABLE table_name (column_name 
data_type ENCODE encoding-type)[, ...]
```

Where *encoding\-type* is taken from the keyword table in the following section\.

For example, the following statement creates a two\-column table, PRODUCT\. When data is loaded into the table, the PRODUCT\_ID column is not compressed, but the PRODUCT\_NAME column is compressed, using the byte dictionary encoding \(BYTEDICT\)\.

```
create table product(
product_id int encode raw,
product_name char(20) encode bytedict);
```

You cannot change the compression encoding for a column after the table is created\. You can specify the encoding for a column when it is added to a table using the ALTER TABLE command\.

```
ALTER TABLE table-name ADD [ COLUMN ] column_name column_type ENCODE encoding-type
```