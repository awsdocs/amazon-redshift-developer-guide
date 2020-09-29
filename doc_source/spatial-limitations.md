# Limitations when using spatial data with Amazon Redshift<a name="spatial-limitations"></a>

The following are limitations when using spatial data with Amazon Redshift: 
+ The maximum size of a `GEOMETRY` object is 1,048,447 bytes\. 
+ Amazon Redshift Spectrum doesn't natively support spatial data\. Therefore, you can't create or alter an external table with a `GEOMETRY` column\. 
+ Data types for Python user\-defined functions \(UDFs\) don't support the `GEOMETRY` data type\. 
+ You can't use a `GEOMETRY` column as a sort key or a distribution key for an Amazon Redshift table\. 
+ You can't use `GEOMETRY` columns in SQL ORDER BY, GROUP BY, or DISTINCT clauses\. 
+ You can't use `GEOMETRY` columns in many SQL functions\. 
+ You can't perform an UNLOAD operation on geometry columns into every format\. You can UNLOAD `GEOMETRY` columns to text or comma\-separated value \(CSV\) files\. Doing this writes `GEOMETRY` data in hexadecimal EWKB format\. If the size of the EWKB data is more than 4 MB, then a warning occurs because the data can't later be loaded into a table\. 
+ The supported compression encoding of `GEOMETRY` data is RAW\. 
+ When using JDBC or ODBC drivers, use customized type mappings\. In this case, the client application must have information on which parameters of a `ResultSet` object are `GEOMETRY` objects\. The `ResultSetMetadata` operation returns type `VARCHAR`\. 

The following nonspatial functions can accept an input of type `GEOMETRY` or columns of type `GEOMETRY`:
+ The aggregate function COUNT
+ The conditional expressions COALESCE and NVL
+ CASE expressions