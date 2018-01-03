# Let COPY Choose Compression Encodings<a name="c_best-practices-use-auto-compression"></a>

You can specify compression encodings when you create a table, but in most cases, automatic compression produces the best results\.

The COPY command will analyze your data and apply compression encodings to an empty table automatically as part of the load operation\. 

Automatic compression balances overall performance when choosing compression encodings\. Range\-restricted scans might perform poorly if sort key columns are compressed much more highly than other columns in the same query\. As a result, automatic compression will choose a less efficient compression encoding to keep the sort key columns balanced with other columns\.

If your table's sort key is a date or timestamp and the table uses many large varchar columns, you might get better performance by not compressing the sort key column at all\. Run the [ANALYZE COMPRESSION](r_ANALYZE_COMPRESSION.md) command on the table, then use the encodings to create a new table, but leave out the compression encoding for the sort key\.

There is a performance cost for automatic compression encoding, but only if the table is empty and does not already have compression encoding\. For short\-lived tables and tables that you create frequently, such as staging tables, load the table once with automatic compression or run the ANALYZE COMPRESSION command, then use those encodings to create new tables\. You can add the encodings to the CREATE TABLE statement, or use CREATE TABLE LIKE to create a new table with the same encoding\. 

For more information, see [Tutorial: Tuning Table Design](tutorial-tuning-tables.md) and [Loading Tables with Automatic Compression](c_Loading_tables_auto_compress.md)\.