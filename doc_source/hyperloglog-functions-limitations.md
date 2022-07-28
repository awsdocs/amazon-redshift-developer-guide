# Limitations<a name="hyperloglog-functions-limitations"></a>

The following are limitations for using HyperLogLog in Amazon Redshift:
+ Amazon Redshift tables don't support an HLLSKETCH column as a sort key or a distribution key for an Amazon Redshift table\.
+ Amazon Redshift doesn't support HLLSKETCH columns in ORDER BY, GROUP BY, or DISTINCT clauses\.
+ You can only UNLOAD HLLSKETCH columns to text or CSV format\. Amazon Redshift then writes the HLLSKETCH data in either a JSON format or a Base64 format\. For more information about UNLOAD, see [UNLOAD](r_UNLOAD.md)\.
+ Amazon Redshift only supports HyperLogLog sketches with a precision \(logm value\) of 15\.
+ JDBC and ODBC drivers don't support the HLLSKETCH data type\. Therefore, the result set uses VARCHAR to represent the HLLSKETCH values\.
+ Amazon Redshift Spectrum doesn't natively support the HLLSKETCH data\. Therefore, you can't create or alter an external table with an HLLSKETCH column\.
+ Data types for Python user\-defined functions \(UDFs\) don't support the HLLSKETCH data type\. For more information about Python UDFs, see [Creating a scalar Python UDF](udf-creating-a-scalar-udf.md)\.