# Let COPY choose compression encodings<a name="c_best-practices-use-auto-compression"></a>

You can specify compression encodings when you create a table, but in most cases,  automatic compression produces the best results\.

The COPY command analyzes your data and applies compression encodings to an empty table automatically as part of the load operation\. 

Automatic compression balances overall performance when choosing compression encodings\. Range\-restricted scans might perform poorly if sort key columns are compressed much more highly than other columns in the same query\. As a result, automatic compression chooses a less efficient compression encoding to keep the sort key columns balanced with other columns\.

Suppose that your table's sort key is a date or timestamp and the table uses many large varchar columns\. In this case, you might get better performance by not compressing the sort key column at all\. Run the [ANALYZE COMPRESSION](r_ANALYZE_COMPRESSION.md) command on the table, then use the encodings to create a new table, but leave out the compression encoding for the sort key\.

There is a performance cost for automatic compression encoding, but only if the table is empty and does not already have compression encoding\. For short\-lived tables and tables that you create frequently, such as staging tables, load the table once with automatic compression or run the ANALYZE COMPRESSION command\. Then use those encodings to create new tables\. You can add the encodings to the CREATE TABLE statement, or use CREATE TABLE LIKE to create a new table with the same encoding\. 

For more information, see [Tutorial: Tuning table design](tutorial-tuning-tables.md) and [Loading tables with automatic compression](c_Loading_tables_auto_compress.md)\.