# Creating User\-Defined Functions<a name="user-defined-functions"></a>

You can create a custom user\-defined scalar function \(UDF\) using either a SQL SELECT clause or a Python program\. The new function is stored in the database and is available for any user with sufficient privileges to run, in much the same way as you run existing Amazon Redshift functions\. 

For Python UDFs, in addition to using the standard Python functionality, you can import your own custom Python modules\. For more information, see [Python Language Support for UDFs](udf-python-language-support.md)\.

By default, all users can execute UDFs\. For more information about privileges, see [UDF Security and Privileges](udf-security-and-privileges.md)\.

**Topics**
+ [UDF Security and Privileges](udf-security-and-privileges.md)
+ [Creating a Scalar SQL UDF](udf-creating-a-scalar-sql-udf.md)
+ [Creating a Scalar Python UDF](udf-creating-a-scalar-udf.md)
+ [Naming UDFs](udf-naming-udfs.md)
+ [Logging Errors and Warnings in UDFs](udf-logging-messages.md)