# CUME\_DIST Window Function Examples<a name="r_Examples_of_CUME_DIST_WF"></a>

The following example calculates the cumulative distribution of the quantity for each seller:

```
select sellerid, qty, cume_dist() 
over (partition by sellerid order by qty) 
from winsales;

sellerid   qty	   cume_dist
--------------------------------------------------
1         10.00	   0.33
1         10.64	   0.67
1         30.37	   1
3         10.04	   0.25
3         15.15	   0.5
3         20.75	   0.75
3         30.55	   1
2         20.09	   0.5
2         20.12	   1
4         10.12	   0.5
4         40.23	   1
```

For a description of the WINSALES table, see [Window Function Examples](r_Window_function_examples.md)\.