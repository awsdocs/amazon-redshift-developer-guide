# RATIO\_TO\_REPORT Window Function Examples<a name="r_Examples_of_RATIO_TO_REPORT_WF"></a>

The following example calculates the ratios of the sales quantities for each seller:

```
select sellerid, qty, ratio_to_report(qty) 
over (partition by sellerid) 
from winsales;

sellerid	qty		ratio_to_report
-------------------------------------------
2		20.12312341      0.5
2		20.08630000      0.5
4		10.12414400      0.2
4		40.23000000      0.8
1		30.37262000      0.6
1		10.64000000      0.21
1		10.00000000      0.2
3		10.03500000      0.13
3		15.14660000      0.2
3		30.54790000      0.4
3		20.74630000      0.27
```

For a description of the WINSALES table, see [Window Function Examples](r_Window_function_examples.md)\.