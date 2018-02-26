# Aggregate Functions<a name="c_Aggregate_Functions"></a>


+ [APPROXIMATE PERCENTILE\_DISC Function](r_APPROXIMATE_PERCENTILE_DISC.md)
+ [AVG Function](r_AVG.md)
+ [COUNT Function](r_COUNT.md)
+ [LISTAGG Function](r_LISTAGG.md)
+ [MAX Function](r_MAX.md)
+ [MEDIAN Function](r_MEDIAN.md)
+ [MIN Function](r_MIN.md)
+ [PERCENTILE\_CONT Function](r_PERCENTILE_CONT.md)
+ [STDDEV\_SAMP and STDDEV\_POP Functions](r_STDDEV_functions.md)
+ [SUM Function](r_SUM.md)
+ [VAR\_SAMP and VAR\_POP Functions](r_VARIANCE_functions.md)

Aggregate functions compute a single result value from a set of input values\. 

SELECT statements using aggregate functions can include two optional clauses: GROUP BY and HAVING\. The syntax for these clauses is as follows \(using the COUNT function as an example\): 

```
SELECT count (*) expression FROM table_reference
WHERE condition [GROUP BY expression ] [ HAVING condition]
```

The GROUP BY clause aggregates and groups results by the unique values in a specified column or columns\. The HAVING clause restricts the results returned to rows where a particular aggregate condition is true, such as count \(\*\) > 1\. The HAVING clause is used in the same way as WHERE to restrict rows based on the value of a column\. See the [COUNT](r_COUNT.md) function description for an example of these additional clauses\.

Aggregate functions don't accept nested aggregate functions or window functions as arguments\.