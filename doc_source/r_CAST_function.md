# CAST and CONVERT functions<a name="r_CAST_function"></a>

You can do runtime conversions between compatible data types by using the CAST and CONVERT functions\. 

Certain data types require an explicit conversion to other data types using the CAST or CONVERT function\. Other data types can be converted implicitly, as part of another command, without using the CAST or CONVERT function\. See [Type compatibility and conversion](c_Supported_data_types.md#r_Type_conversion)\. 

## CAST<a name="r_CAST_function-cast"></a>

You can use two equivalent syntax forms to cast expressions from one data type to another: 

```
CAST ( expression AS type )
expression :: type
```

## Arguments<a name="r_CAST_function-arguments"></a>

 *expression*   
An expression that evaluates to one or more values, such as a column name or a literal\. Converting null values returns nulls\. The expression cannot contain blank or empty strings\. 

 *type*   
One of the supported [Data types](c_Supported_data_types.md)\. 

## Return type<a name="r_CAST_function-return-type"></a>

CAST returns the data type specified by the *type* argument\. 

**Note**  
Amazon Redshift returns an error if you try to perform a problematic conversion such as the following DECIMAL conversion that loses precision:   

```
select 123.456::decimal(2,1);
```
or an INTEGER conversion that causes an overflow:   

```
select 12345678::smallint;
```

## CONVERT<a name="convert-function"></a>

You can also use the CONVERT function to convert values from one data type to another: 

```
CONVERT ( type, expression )
```

## Arguments<a name="r_CAST_function-arguments2"></a>

 *type*   
One of the supported [Data types](c_Supported_data_types.md)\. 

 *expression*   
An expression that evaluates to one or more values, such as a column name or a literal\. Converting null values returns nulls\. The expression cannot contain blank or empty strings\. 

## Return type<a name="r_CAST_function-return-type2"></a>

CONVERT returns the data type specified by the *type* argument\. 

## Examples<a name="r_CAST_function-examples"></a>

The following two queries are equivalent\. They both cast a decimal value to an integer: 

```
select cast(pricepaid as integer)
from sales where salesid=100;

pricepaid
-----------
162
(1 row)
```

```
select pricepaid::integer
from sales where salesid=100;

pricepaid
-----------
162
(1 row)
```

The following query uses the CONVERT function to return the same result: 

```
select convert(integer, pricepaid)
from sales where salesid=100;

pricepaid
-----------
162
(1 row)
```

In this example, the values in a timestamp column are cast as dates: 

```
select cast(saletime as date), salesid
from sales order by salesid limit 10;

 saletime  | salesid
-----------+---------
2008-02-18 |       1
2008-06-06 |       2
2008-06-06 |       3
2008-06-09 |       4
2008-08-31 |       5
2008-07-16 |       6
2008-06-26 |       7
2008-07-10 |       8
2008-07-22 |       9
2008-08-06 |      10
(10 rows)
```

In this example, the values in a date column are cast as timestamps: 

```
select cast(caldate as timestamp), dateid
from date order by dateid limit 10;

      caldate       | dateid
--------------------+--------
2008-01-01 00:00:00 |   1827
2008-01-02 00:00:00 |   1828
2008-01-03 00:00:00 |   1829
2008-01-04 00:00:00 |   1830
2008-01-05 00:00:00 |   1831
2008-01-06 00:00:00 |   1832
2008-01-07 00:00:00 |   1833
2008-01-08 00:00:00 |   1834
2008-01-09 00:00:00 |   1835
2008-01-10 00:00:00 |   1836
(10 rows)
```

In this example, an integer is cast as a character string: 

```
select cast(2008 as char(4));
bpchar
--------
2008
```

In this example, a DECIMAL\(6,3\) value is cast as a DECIMAL\(4,1\) value: 

```
select cast(109.652 as decimal(4,1));
numeric
---------
109.7
```

In this example, the PRICEPAID column \(a DECIMAL\(8,2\) column\) in the SALES table is converted to a DECIMAL\(38,2\) column and the values are multiplied by 100000000000000000000\. 

```
select salesid, pricepaid::decimal(38,2)*100000000000000000000
as value from sales where salesid<10 order by salesid;

 salesid |           value
---------+----------------------------
       1 | 72800000000000000000000.00
       2 |  7600000000000000000000.00
       3 | 35000000000000000000000.00
       4 | 17500000000000000000000.00
       5 | 15400000000000000000000.00
       6 | 39400000000000000000000.00
       7 | 78800000000000000000000.00
       8 | 19700000000000000000000.00
       9 | 59100000000000000000000.00
(9 rows)
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