# HLL\_CARDINALITY function<a name="r_HLL_CARDINALITY"></a>

The HLL\_CARDINALITY function returns the cardinality of the input HLLSKETCH data type\.

## Syntax<a name="r_HLL_CARDINALITY-synopsis"></a>

```
HLL_CARDINALITY (hllsketch_expression)
```

## Argument<a name="r_HLL_CARDINALITY-argument"></a>

 *hllsketch\_expression*   
Any valid expression that evaluates to an HLLSKETCH type, such as a column name\. The input value is the HLLSKETCH data type\.

## Return type<a name="r_HLL_CARDINALITY-return-type"></a>

The HLL\_CARDINALITY function returns a BIGINT or INT8 value\.

## Examples<a name="r_HLL_CARDINALITY-examples"></a>

The following example returns the cardinality of column `sketch` in table `hll_table`\.

```
CREATE TABLE a_table(an_int INT, b_int INT);
INSERT INTO a_table VALUES (1,1), (2,1), (3,1), (4,1), (1,2), (2,2), (3,2), (4,2), (5,2), (6,2);

CREATE TABLE hll_table (sketch HLLSKETCH);
INSERT INTO hll_table select hll_create_sketch(an_int) from a_table group by b_int;

SELECT hll_cardinality(sketch) AS cardinality FROM hll_table;
cardinality
-------------
6
4
(2 rows)
```