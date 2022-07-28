# Creating user\-defined functions<a name="user-defined-functions"></a>

You can create a custom scalar user\-defined function \(UDF\) using either a SQL SELECT clause or a Python program\. The new function is stored in the database and is available for any user with sufficient privileges to run\. You run a custom scalar UDF in much the same way as you run existing Amazon Redshift functions\. 

For Python UDFs, in addition to using the standard Python functionality, you can import your own custom Python modules\. For more information, see [Python language support for UDFs](udf-python-language-support.md)\.

You can also create AWS Lambda UDFs that use custom functions defined in Lambda as part of your SQL queries\. Lambda UDFs enable you to write complex UDFs and integrate with third\-party components\. They also can help you overcome some of the limitations of current Python and SQL UDFs\. For example, they can help you access network and storage resources and write more full\-fledged SQL statements\. You can create Lambda UDFs in any of the programming languages supported by Lambda, such as Java, Go, PowerShell, Node\.js, C\#, Python, and Ruby\. Or you can use a custom runtime\.

By default, all users can run UDFs\. For more information about privileges, see [UDF security and privileges](udf-security-and-privileges.md)\.

**Topics**
+ [UDF security and privileges](udf-security-and-privileges.md)
+ [Creating a scalar SQL UDF](udf-creating-a-scalar-sql-udf.md)
+ [Creating a scalar Python UDF](udf-creating-a-scalar-udf.md)
+ [Creating a scalar Lambda UDF](udf-creating-a-lambda-sql-udf.md)
+ [Example uses of user\-defined functions \(UDFs\)](udf-example-uses.md)