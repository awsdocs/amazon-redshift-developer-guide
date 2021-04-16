# Computations with numeric values<a name="r_numeric_computations201"></a>

In this context, *computation* refers to binary mathematical operations: addition, subtraction, multiplication, and division\. This section describes the expected return types for these operations, as well as the specific formula that is applied to determine precision and scale when DECIMAL data types are involved\. 

When numeric values are computed during query processing, you might encounter cases where the computation is impossible and the query returns a numeric overflow error\. You might also encounter cases where the scale of computed values varies or is unexpected\. For some operations, you can use explicit casting \(type promotion\) or Amazon Redshift configuration parameters to work around these problems\. 

For information about the results of similar computations with SQL functions, see [Aggregate functions](c_Aggregate_Functions.md)\. 

## Return types for computations<a name="r_numeric_computations201-return-types-for-computations"></a>

Given the set of numeric data types supported in Amazon Redshift, the following table shows the expected return types for addition, subtraction, multiplication, and division operations\. The first column on the left side of the table represents the first operand in the calculation, and the top row represents the second operand\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_numeric_computations201.html)

## Precision and scale of computed DECIMAL results<a name="r_numeric_computations201-precision-and-scale-of-computed-decimal-results"></a>

The following table summarizes the rules for computing resulting precision and scale when mathematical operations return DECIMAL results\. In this table, `p1` and `s1` represent the precision and scale of the first operand in a calculation and `p2` and `s2` represent the precision and scale of the second operand\. \(Regardless of these calculations, the maximum result precision is 38, and the maximum result scale is 38\.\) 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_numeric_computations201.html)

For example, the PRICEPAID and COMMISSION columns in the SALES table are both DECIMAL\(8,2\) columns\. If you divide PRICEPAID by COMMISSION \(or vice versa\), the formula is applied as follows: 

```
Precision = 8-2 + 2 + max(4,2+8-2+1)
= 6 + 2 + 9 = 17

Scale = max(4,2+8-2+1) = 9

Result = DECIMAL(17,9)
```

The following calculation is the general rule for computing the resulting precision and scale for operations performed on DECIMAL values with set operators such as UNION, INTERSECT, and EXCEPT or functions such as COALESCE and DECODE: 

```
Scale = max(s1,s2)
Precision = min(max(p1-s1,p2-s2)+scale,19)
```

For example, a DEC1 table with one DECIMAL\(7,2\) column is joined with a DEC2 table with one DECIMAL\(15,3\) column to create a DEC3 table\. The schema of DEC3 shows that the column becomes a NUMERIC\(15,3\) column\. 

```
create table dec3 as select * from dec1 union select * from dec2;
```

Result 

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 'dec3';

column |     type      | encoding | distkey | sortkey 
-------+---------------+----------+---------+---------
c1     | numeric(15,3) | none     | f       | 0
```

In the above example, the formula is applied as follows: 

```
Precision = min(max(7-2,15-3) + max(2,3), 19)
= 12 + 3 = 15

Scale = max(2,3) = 3

Result = DECIMAL(15,3)
```

## Notes on division operations<a name="r_numeric_computations201-notes-on-division-operations"></a>

For division operations, divide\-by\-zero conditions return errors\. 

The scale limit of 100 is applied after the precision and scale are calculated\. If the calculated result scale is greater than 100, division results are scaled as follows:
+ Precision = ` precision - (scale - max_scale)` 
+ Scale = ` max_scale ` 

If the calculated precision is greater than the maximum precision \(38\), the precision is reduced to 38, and the scale becomes the result of: `max(38 + scale - precision), min(4, 100))` 

## Overflow conditions<a name="r_numeric_computations201-overflow-conditions"></a>

Overflow is checked for all numeric computations\. DECIMAL data with a precision of 19 or less is stored as 64\-bit integers\. DECIMAL data with a precision that is greater than 19 is stored as 128\-bit integers\. The maximum precision for all DECIMAL values is 38, and the maximum scale is 37\. Overflow errors occur when a value exceeds these limits, which apply to both intermediate and final result sets: 
+ Explicit casting results in runtime overflow errors when specific data values do not fit the requested precision or scale specified by the cast function\. For example, you cannot cast all values from the PRICEPAID column in the SALES table \(a DECIMAL\(8,2\) column\) and return a DECIMAL\(7,3\) result: 

  ```
  select pricepaid::decimal(7,3) from sales;
  ERROR:  Numeric data overflow (result precision)
  ```

  This error occurs because *some* of the larger values in the PRICEPAID column cannot be cast\.
+ Multiplication operations produce results in which the result scale is the sum of the scale of each operand\. If both operands have a scale of 4, for example, the result scale is 8, leaving only 10 digits for the left side of the decimal point\. Therefore, it is relatively easy to run into overflow conditions when multiplying two large numbers that both have significant scale\.

## Numeric calculations with INTEGER and DECIMAL types<a name="r_numeric_computations201-numeric-calculations-with-integer-and-decimal-types"></a>

When one of the operands in a calculation has an INTEGER data type and the other operand is DECIMAL, the INTEGER operand is implicitly cast as a DECIMAL: 
+ INT2 \(SMALLINT\) is cast as DECIMAL\(5,0\) 
+ INT4 \(INTEGER\) is cast as DECIMAL\(10,0\) 
+ INT8 \(BIGINT\) is cast as DECIMAL\(19,0\) 

For example, if you multiply SALES\.COMMISSION, a DECIMAL\(8,2\) column, and SALES\.QTYSOLD, a SMALLINT column, this calculation is cast as: 

```
DECIMAL(8,2) * DECIMAL(5,0)
```