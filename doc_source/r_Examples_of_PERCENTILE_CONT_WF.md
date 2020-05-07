# PERCENTILE\_CONT window function examples<a name="r_Examples_of_PERCENTILE_CONT_WF"></a>

The following examples uses the WINSALES table\. For a description of the WINSALES table, see [Window function examples](r_Window_function_examples.md)\. 

```
select sellerid, qty, percentile_cont(0.5) 
within group (order by qty) 
over() as median from winsales;

 sellerid | qty | median 
----------+-----+--------
        1 |  10 |   20.0
        1 |  10 |   20.0
        3 |  10 |   20.0
        4 |  10 |   20.0
        3 |  15 |   20.0
        2 |  20 |   20.0
        3 |  20 |   20.0
        2 |  20 |   20.0
        3 |  30 |   20.0
        1 |  30 |   20.0
        4 |  40 |   20.0
(11 rows)
```

```
select sellerid, qty, percentile_cont(0.5) 
within group (order by qty) 
over(partition by sellerid) as median from winsales;

 sellerid | qty | median 
----------+-----+--------
        2 |  20 |   20.0
        2 |  20 |   20.0
        4 |  10 |   25.0
        4 |  40 |   25.0
        1 |  10 |   10.0
        1 |  10 |   10.0
        1 |  30 |   10.0
        3 |  10 |   17.5
        3 |  15 |   17.5
        3 |  20 |   17.5
        3 |  30 |   17.5
(11 rows)
```

The following example calculates the PERCENTILE\_CONT and PERCENTILE\_DISC of the ticket sales for sellers in Washington state\. 

```
SELECT sellerid, state, sum(qtysold*pricepaid) sales, 
percentile_cont(0.6) within group (order by sum(qtysold*pricepaid::decimal(14,2) ) desc) over(),
percentile_disc(0.6) within group (order by sum(qtysold*pricepaid::decimal(14,2) ) desc) over()
from sales s, users u 
where s.sellerid = u.userid and state = 'WA' and sellerid < 1000
group by sellerid, state;

 sellerid | state |  sales  | percentile_cont | percentile_disc
----------+-------+---------+-----------------+-----------------
      127 | WA    | 6076.00 |         2044.20 |         1531.00
      787 | WA    | 6035.00 |         2044.20 |         1531.00
      381 | WA    | 5881.00 |         2044.20 |         1531.00
      777 | WA    | 2814.00 |         2044.20 |         1531.00
       33 | WA    | 1531.00 |         2044.20 |         1531.00
      800 | WA    | 1476.00 |         2044.20 |         1531.00
        1 | WA    | 1177.00 |         2044.20 |         1531.00
(7 rows)
```