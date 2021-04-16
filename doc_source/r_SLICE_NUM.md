# SLICE\_NUM Function<a name="r_SLICE_NUM"></a>

Returns an integer corresponding to the slice number in the cluster where the data for a row is located\. SLICE\_NUM takes no parameters\. 

## Syntax<a name="r_SLICE_NUM-syntax"></a>

```
SLICE_NUM()
```

## Return type<a name="r_SLICE_NUM-return-type"></a>

The SLICE\_NUM function returns an integer\. 

## Examples<a name="r_SLICE_NUM-examples"></a>

The following example shows which slices contain data for the first ten EVENT rows in the EVENTS table: 

```
select distinct eventid, slice_num() from event order by eventid limit 10;

 eventid | slice_num
---------+-----------
       1 |         1
       2 |         2
       3 |         3
       4 |         0
       5 |         1
       6 |         2
       7 |         3
       8 |         0
       9 |         1
      10 |         2
(10 rows)
```

The following example returns a code \(10000\) to show that a query without a FROM statement executes on the leader node: 

```
select slice_num();
slice_num
-----------
10000
(1 row)
```