# HLL\_CREATE\_SKETCH function<a name="r_HLL_CREATE_SKETCH"></a>

The HLL\_CREATE\_SKETCH function returns an HLLSKETCH data type that encapsulates the input expression values\. The HLL\_CREATE\_SKETCH function works with any data type and ignores NULL values\. When there are no rows in a table or all rows are NULL, the resulting sketch has no index\-value pairs such as `{"version":1,"logm":15,"sparse":{"indices":[],"values":[]}}`\.

## Syntax<a name="r_HLL_CREATE_SKETCH-synopsis"></a>

```
HLL_CREATE_SKETCH (aggregate_expression)
```

## Argument<a name="r_HLL_CREATE_SKETCH-argument"></a>

 *aggregate\_expression*   
Any valid expression that provides the value to an aggregate, such as a column name\. NULL values are ignored\. This function supports any data type as input except HLLSKETCH and GEOMETRY\.

## Return type<a name="r_HLL_CREATE_SKETCH-return-type"></a>

The HLL\_CREATE\_SKETCH function returns an HLLSKETCH value\.

## Examples<a name="r_HLL_CREATE_SKETCH-examples"></a>

The following example returns the HLLSKETCH type for column `an_int` in table `a_table`\. A JSON object is used to represent a sparse HyperLogLog sketch when importing, exporting, or printing sketches\. A string representation \(in Base64 format\) is used to represent a dense HyperLogLog sketch\.

```
CREATE TABLE a_table(an_int INT);
INSERT INTO a_table VALUES (1), (2), (3), (4);

SELECT hll_create_sketch(an_int) AS sketch FROM a_table;
sketch
-------------------------------------------------------------------------------------------------------
{"version":1,"logm":15,"sparse":{"indices":[20812342,20850007,22362299,47158030],"values":[1,2,1,1]}}
(1 row)
```