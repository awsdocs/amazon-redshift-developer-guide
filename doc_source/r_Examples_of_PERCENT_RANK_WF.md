# PERCENT\_RANK window function examples<a name="r_Examples_of_PERCENT_RANK_WF"></a>

The following example calculates the percent rank of the sales quantities for each seller:

```
select sellerid, qty, percent_rank() 
over (partition by sellerid order by qty) 
from winsales;

sellerid	qty		percent_rank
----------------------------------------
1		10.00		0.0
1		10.64		0.5
1		30.37		1.0
3		10.04		0.0
3		15.15		0.33
3		20.75		0.67
3		30.55		1.0
2		20.09		0.0
2		20.12		1.0
4		10.12		0.0
4		40.23		1.0
```

For a description of the WINSALES table, see [Window function examples](r_Window_function_examples.md)\.