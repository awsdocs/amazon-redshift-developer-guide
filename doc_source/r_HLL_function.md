# HLL function<a name="r_HLL_function"></a>

The HLL function returns the HyperLogLog cardinality of the input expression values\. The HLL function works with any data types except the HLLSKETCH data type\. The HLL function ignores NULL values\. When there are no rows in a table or all rows are NULL, the resulting cardinality is 0\.

## Syntax<a name="r_HLL_function-synopsis"></a>

```
HLL (aggregate_expression)
```

## Argument<a name="r_HLL_function-argument"></a>

 *aggregate\_expression*   
Any valid expression that provides the value to an aggregate, such as a column name\. This function supports any data type as input except HLLSKETCH and GEOMETRY\.

## Return type<a name="r_HLL_function-return-type"></a>

The HLL function returns a BIGINT or INT8 value\.

## Examples<a name="r_HLL_function-examples"></a>

The following example returns the cardinality of column `an_int` in table `a_table`\.

```
CREATE TABLE a_table(an_int INT);
INSERT INTO a_table VALUES (1), (2), (3), (4);

SELECT hll(an_int) AS cardinality FROM a_table;
cardinality
-------------
4
```