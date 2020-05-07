# Creating user\-defined functions<a name="user-defined-functions"></a>

You can create a custom scalar user\-defined function \(UDF\) using either a SQL SELECT clause or a Python program\. The new function is stored in the database and is available for any user with sufficient privileges to run, in much the same way as you run existing Amazon Redshift functions\. 

For Python UDFs, in addition to using the standard Python functionality, you can import your own custom Python modules\. For more information, see [Python language support for UDFs](udf-python-language-support.md)\.

By default, all users can execute UDFs\. For more information about privileges, see [UDF security and privileges](udf-security-and-privileges.md)\.

**Topics**
+ [UDF security and privileges](udf-security-and-privileges.md)
+ [Creating a scalar SQL UDF](udf-creating-a-scalar-sql-udf.md)
+ [Creating a scalar Python UDF](udf-creating-a-scalar-udf.md)
+ [Naming UDFs](udf-naming-udfs.md)
+ [Logging errors and warnings in UDFs](udf-logging-messages.md)