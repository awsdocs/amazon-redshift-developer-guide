# HLL\_COMBINE function<a name="r_HLL_COMBINE"></a>

The HLL\_COMBINE aggregate function returns an HLLSKETCH data type that combines all input HLLSKETCH values\. 

The combination of two or more HyperLogLog sketches is a new HLLSKETCH that encapsulates information about the union of the distinct values that each input sketch represents\. After combining sketches, Amazon Redshift extracts the cardinality of the union of two or more datasets\. For more information on how to combine multiple sketches, see [Example: Return a HyperLogLog sketch from combining multiple sketches](r_HLL-examples.md#hll-examples-multiple-sketches)\.

## Syntax<a name="r_HLL_COMBINE-synopsis"></a>

```
HLL_COMBINE (hllsketch_expression)
```

## Argument<a name="r_HLL_COMBINE-argument"></a>

 *hllsketch\_expression*   
Any valid expression that evaluates to an HLLSKETCH type, such as a column name\. The input value is the HLLSKETCH data type\.

## Return type<a name="r_HLL_COMBINE-return-type"></a>

The HLL\_COMBINE function returns an HLLSKETCH type\.

## Examples<a name="r_HLL_COMBINE-examples"></a>

The following example returns the combined HLLSKETCH values in the table `hll_table`\.

```
CREATE TABLE a_table(an_int INT, b_int INT);
INSERT INTO a_table VALUES (1,1), (2,1), (3,1), (4,1), (1,2), (2,2), (3,2), (4,2), (5,2), (6,2);

CREATE TABLE hll_table (sketch HLLSKETCH);
INSERT INTO hll_table select hll_create_sketch(an_int) from a_table group by b_int;

SELECT hll_combine(sketch) AS sketches FROM hll_table;
sketches
----------------------------------------------------------------------------------------------------------------------------
{"version":1,"logm":15,"sparse":{"indices":[20812342,20850007,22362299,40314817,42650774,47158030],"values":[1,2,1,3,2,1]}}
(1 row)
```