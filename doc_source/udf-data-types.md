# Python UDF data types<a name="udf-data-types"></a>

Python UDFs can use any standard Amazon Redshift data type for the input arguments and the function's return value\. In addition to the standard data types, UDFs support the data type *ANYELEMENT*, which Amazon Redshift automatically converts to a standard data type based on the arguments supplied at run time\. Scalar UDFs can return a data type of ANYELEMENT\. For more information, see [ANYELEMENT data type](udf-creating-a-scalar-udf.md#udf-anyelement-data-type)\.

During execution, Amazon Redshift converts the arguments from Amazon Redshift data types to Python data types for processing, and then converts the return value from the Python data type to the corresponding Amazon Redshift data type\. For more information about Amazon Redshift data types, see [Data types](c_Supported_data_types.md)\.

The following table maps Amazon Redshift data types to Python data types\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/udf-data-types.html)