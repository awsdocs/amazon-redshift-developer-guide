# CONVERT function<a name="r_CONVERT_function"></a>

Like the [CAST function](https://docs.aws.amazon.com/redshift/latest/dg/r_CAST_function.html), the CONVERT function converts one data type to another compatible data type\. For instance, you can convert a string to a date, or a numeric type to a string\. CONVERT performs a runtime conversion, which means that the conversion doesn't change a value's data type in a source table\. It's changed only in the context of the query\.

Certain data types require an explicit conversion to other data types using the CONVERT function\. Other data types can be converted implicitly, as part of another command, without using CAST or CONVERT\. See [Type compatibility and conversion](c_Supported_data_types.md#r_Type_conversion)\. 

## Syntax<a name="r_CONVERT-syntax"></a>

```
CONVERT ( type, expression )
```

## Arguments<a name="r_CONVERT-arguments"></a>

 *type*   
One of the supported [Data types](c_Supported_data_types.md)\. 

 *expression*   
An expression that evaluates to one or more values, such as a column name or a literal\. Converting null values returns nulls\. The expression cannot contain blank or empty strings\. 

## Return type<a name="r_CONVERT-return-type"></a>

CONVERT returns the data type specified by the *type* argument\.

**Note**  
Amazon Redshift returns an error if you try to perform a problematic conversion, such as a DECIMAL conversion that loses precision, like the following:   

```
SELECT CONVERT(decimal(2,1), 123.456);
```
or an INTEGER conversion that causes an overflow:   

```
SELECT CONVERT(smallint, 12345678);
```

## Examples<a name="r_CONVERT-examples"></a>

Some of the examples use the sample [TICKIT database](https://docs.aws.amazon.com/redshift/latest/dg/c_sampledb.html)\. For more information about setting up sample data, see [ Getting started with Amazon Redshift clusters and data loading](https://docs.aws.amazon.com/redshift/latest/gsg/data-loading.html)\.

The following query uses the CONVERT function to convert a column of decimals into integers

```
SELECT CONVERT(integer, pricepaid)
FROM sales WHERE salesid=100;
```

This example converts an integer into a character string\.

```
SELECT CONVERT(char(4), 2008);
```

In this example, the current date and time is converted to a variable character data type: 

```
SELECT CONVERT(VARCHAR(30), GETDATE());

getdate
---------
2023-02-02 04:31:16
```

This example converts the saletime column into just the time, removing the dates from each row\.

```
SELECT CONVERT(time, saletime), salesid
FROM sales order by salesid limit 10;
```

The following example converts variable character data into a datetime object\.

```
SELECT CONVERT(datetime, '2008-02-18 02:36:48') as mysaletime;
```

**Note**  
You can't perform a CAST or CONVERT operation on the `GEOMETRY` data type to change it to another data type\. However, you can provide a hexadecimal representation of a string literal in extended well\-known binary \(EWKB\) format as input to functions that accept a `GEOMETRY` argument\. For example, the `ST_AsText` function following expects a `GEOMETRY` data type\.   

```
SELECT ST_AsText('01010000000000000000001C400000000000002040');
```

```
st_astext  
------------
 POINT(7 8)
```
You can also explicitly specify the `GEOMETRY` data type\.   

```
SELECT ST_AsText('010100000000000000000014400000000000001840'::geometry);
```

```
st_astext  
------------
 POINT(5 6)
```