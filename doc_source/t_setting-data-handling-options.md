# Setting data handling options<a name="t_setting-data-handling-options"></a>

You can set table parameters when you create external tables to tailor the data being queried in external tables\. Otherwise, scan errors can occur\. For more information, see TABLE PROPERTIES in [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md)\. For examples, see [Data handling examples](r_CREATE_EXTERNAL_TABLE_examples.md#r_CREATE_EXTERNAL_TABLE_examples-data-handling)\. For a list of errors, see [SVL\_SPECTRUM\_SCAN\_ERROR](r_SVL_SPECTRUM_SCAN_ERROR.md)\.

You can set the following TABLE PROPERTIES when you create external tables to specify input handling for data being queried in external tables\.
+ `column_count_mismatch_handling` to identify if the file contains less or more values for a row than the number of columns specified in the external table definition\. 
+ `invalid_char_handling` to specify input handling for invalid characters in columns containing VARCHAR, CHAR, and string data\. When you specify REPLACE for `invalid_char_handling`, you can specify the replacement character to use\.
+ `numeric_overflow_handling` to specify cast overflow handling in columns containing integer and decimal data\.
+ `surplus_bytes_handling` to specify input handling for surplus bytes in columns containing VARBYTE data\. 
+ `surplus_char_handling` to specify input handling for surplus characters in columns containing VARCHAR, CHAR, and string data\.

You can set a configuration option to cancel queries that exceed a maximum number of errors\. For more information, see [spectrum\_query\_maxerror](r_spectrum_query_maxerror.md)\.