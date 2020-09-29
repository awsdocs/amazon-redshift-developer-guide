# Considerations<a name="hyperloglog-functions-usage-notes"></a>

The following are considerations for using HyperLogLog in Amazon Redshift:
+ The following non\-HyperLogLog functions can accept an input of type HLLSKETCH or columns of type HLLSKETCH:
  + The aggregate function COUNT
  + The conditional expressions COALESCE and NVL
  + CASE expressions
+ The supported encoding is RAW\.
+ You can perform an UNLOAD operation on table with HLLSKETCH columns into text or CSV\. You can use the UNLOAD HLLSKETCH columns to write HLLSKETCH data\. Amazon Redshift shows the data in a JSON format for a sparse representation or a Base64 format for a dense representation\. For more information about UNLOAD, see [Unloading the HLLSKETCH data type](r_UNLOAD.md#unload-usage-hll)\.

  The following shows the format used for a sparse HyperLogLog sketch represented in a JSON format\.

  ```
  {"version":1,"logm":15,"sparse":{"indices":[15099259,33107846,37891580,50065963],"values":[2,3,2,1]}}
  ```
+ You can import text or CSV data into Amazon Redshift using the COPY command\. For more information, see [Loading the HLLSKETCH data type](copy-usage_notes-hll.md)\.