# PERCENTILE\_DISC Window Function Examples<a name="r_Examples_of_PERCENTILE_DISC_WF"></a>

The following examples uses the WINSALES table\. For a description of the WINSALES table, see [Window Function Examples](r_Window_function_examples.md)\. 

```
select sellerid, qty, percentile_disc(0.5) 
within group (order by qty) 
over() as median from winsales;

sellerid | qty | median
----------+-----+--------
        1 |  10 |     20
        3 |  10 |     20
        1 |  10 |     20
        4 |  10 |     20
        3 |  15 |     20
        2 |  20 |     20
        2 |  20 |     20
        3 |  20 |     20
        1 |  30 |     20
        3 |  30 |     20
        4 |  40 |     20
(11 rows)
```

```
select sellerid, qty, percentile_disc(0.5) 
within group (order by qty) 
over(partition by sellerid) as median from winsales;

sellerid | qty | median
----------+-----+--------
        2 |  20 |     20
        2 |  20 |     20
        4 |  10 |     10
        4 |  40 |     10
        1 |  10 |     10
        1 |  10 |     10
        1 |  30 |     10
        3 |  10 |     15
        3 |  15 |     15
        3 |  20 |     15
        3 |  30 |     15
(11 rows) 
```