# MEDIAN Window Function Examples<a name="r_Examples_of_median_WF"></a>

 The following example calculates the median sales quantity for each seller: 

```
select sellerid, qty, median(qty) 
over (partition by sellerid) 
from winsales
order by sellerid;


sellerid	qty	median
---------------------------
1		10	10.0
1		10	10.0
1		30	10.0
2		20	20.0
2		20	20.0
3		10	17.5
3		15	17.5
3		20	17.5
3		30	17.5
4		10	25.0
4		40	25.0
```

For a description of the WINSALES table, see [Window Function Examples](r_Window_function_examples.md)\. 