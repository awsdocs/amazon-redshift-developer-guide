# Mostly encoding<a name="c_MostlyN_encoding"></a>

Mostly encodings are useful when the data type for a column is larger than most of the stored values require\. By specifying a mostly encoding for this type of column, you can compress the majority of the values in the column to a smaller standard storage size\. The remaining values that cannot be compressed are stored in their raw form\. For example, you can compress a 16\-bit column, such as an INT2 column, to 8\-bit storage\.

In general, the mostly encodings work with the following data types:
+ SMALLINT/INT2 \(16\-bit\)
+ INTEGER/INT \(32\-bit\)
+ BIGINT/INT8 \(64\-bit\)
+ DECIMAL/NUMERIC \(64\-bit\)

Choose the appropriate variation of the mostly encoding to suit the size of the data type for the column\. For example, apply MOSTLY8 to a column that is defined as a 16\-bit integer column\. Applying MOSTLY16 to a column with a 16\-bit data type or MOSTLY32 to a column with a 32\-bit data type is disallowed\.

Mostly encodings might be less effective than no compression when a relatively high number of the values in the column cannot be compressed\. Before applying one of these encodings to a column, check that *most* of the values that you are going to load now \(and are likely to load in the future\) fit into the ranges shown in the following table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_MostlyN_encoding.html)

**Note**  
For decimal values, ignore the decimal point to determine whether the value fits into the range\. For example, 1,234\.56 is treated as 123,456 and can be compressed in a MOSTLY32 column\.

For example, the VENUEID column in the VENUE table is defined as a raw integer column, which means that its values consume 4 bytes of storage\. However, the current range of values in the column is **0** to **309**\. Therefore, re\-creating and reloading this table with MOSTLY16 encoding for VENUEID would reduce the storage of every value in that column to 2 bytes\.

If the VENUEID values referenced in another table were mostly in the range of 0 to 127, it might make sense to encode that foreign\-key column as MOSTLY8\. Before making the choice, you would have to run some queries against the referencing table data to find out whether the values mostly fall into the 8\-bit, 16\-bit, or 32\-bit range\.

The following table shows compressed sizes for specific numeric values when the MOSTLY8, MOSTLY16, and MOSTLY32 encodings are used:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_MostlyN_encoding.html)