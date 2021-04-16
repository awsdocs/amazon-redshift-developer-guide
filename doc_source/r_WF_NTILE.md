# NTILE window function<a name="r_WF_NTILE"></a>

 The NTILE window function divides ordered rows in the partition into the specified number of ranked groups of as equal size as possible and returns the group that a given row falls into\. 

## Syntax<a name="r_WF_NTILE-synopsis"></a>

```
NTILE (expr)
OVER ( 
[ PARTITION BY expression_list ] 
[ ORDER BY order_list ]
)
```

## Arguments<a name="r_WF_NTILE-arguments"></a>

 *expr*   
The number of ranking groups and must result in a positive integer value \(greater than 0\) for each partition\. The *expr* argument must not be nullable\. 

OVER   
 A clause that specifies the window partitioning and ordering\. The OVER clause cannot contain a window frame specification\. 

PARTITION BY *window\_partition*   
Optional\. The range of records for each group in the OVER clause\. 

ORDER BY *window\_ordering*   
Optional\. An expression that sorts the rows within each partition\. If the ORDER BY clause is omitted, the ranking behavior is the same\.  
If ORDER BY does not produce a unique ordering, the order of the rows is nondeterministic\. For more information, see [Unique ordering of data for window functions](r_Examples_order_by_WF.md)\. 

## Return type<a name="r_WF_NTILE-return-type"></a>

BIGINT

## Examples<a name="r_WF_NTILE-examples"></a>

 The following example ranks into four ranking groups the price paid for Hamlet tickets on August 26, 2008\. The result set is 17 rows, divided almost evenly among the rankings 1 through 4: 

```
select eventname, caldate, pricepaid, ntile(4)
over(order by pricepaid desc) from sales, event, date
where sales.eventid=event.eventid and event.dateid=date.dateid and eventname='Hamlet'
and caldate='2008-08-26'
order by 4;

eventname |  caldate   | pricepaid | ntile
-----------+------------+-----------+-------
Hamlet    | 2008-08-26 |   1883.00 |     1
Hamlet    | 2008-08-26 |   1065.00 |     1
Hamlet    | 2008-08-26 |    589.00 |     1
Hamlet    | 2008-08-26 |    530.00 |     1
Hamlet    | 2008-08-26 |    472.00 |     1
Hamlet    | 2008-08-26 |    460.00 |     2
Hamlet    | 2008-08-26 |    355.00 |     2
Hamlet    | 2008-08-26 |    334.00 |     2
Hamlet    | 2008-08-26 |    296.00 |     2
Hamlet    | 2008-08-26 |    230.00 |     3
Hamlet    | 2008-08-26 |    216.00 |     3
Hamlet    | 2008-08-26 |    212.00 |     3
Hamlet    | 2008-08-26 |    106.00 |     3
Hamlet    | 2008-08-26 |    100.00 |     4
Hamlet    | 2008-08-26 |     94.00 |     4
Hamlet    | 2008-08-26 |     53.00 |     4
Hamlet    | 2008-08-26 |     25.00 |     4
(17 rows)
```