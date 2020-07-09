# Aggregate functions<a name="c_Aggregate_Functions"></a>

**Topics**
+ [APPROXIMATE PERCENTILE\_DISC function](r_APPROXIMATE_PERCENTILE_DISC.md)
+ [AVG function](r_AVG.md)
+ [COUNT function](r_COUNT.md)
+ [LISTAGG function](r_LISTAGG.md)
+ [MAX function](r_MAX.md)
+ [MEDIAN function](r_MEDIAN.md)
+ [MIN function](r_MIN.md)
+ [PERCENTILE\_CONT function](r_PERCENTILE_CONT.md)
+ [STDDEV\_SAMP and STDDEV\_POP functions](r_STDDEV_functions.md)
+ [SUM function](r_SUM.md)
+ [VAR\_SAMP and VAR\_POP functions](r_VARIANCE_functions.md)

Aggregate functions compute a single result value from a set of input values\. 

SELECT statements using aggregate functions can include two optional clauses: GROUP BY and HAVING\. The syntax for these clauses is as follows \(using the COUNT function as an example\): 

```
SELECT count (*) expression FROM table_reference
WHERE condition [GROUP BY expression ] [ HAVING condition]
```

The GROUP BY clause aggregates and groups results by the unique values in a specified column or columns\. The HAVING clause restricts the results returned to rows where a particular aggregate condition is true, such as count \(\*\) > 1\. The HAVING clause is used in the same way as WHERE to restrict rows based on the value of a column\. For an example of these additional clauses, see the [COUNT](r_COUNT.md)\.

Aggregate functions don't accept nested aggregate functions or window functions as arguments\.