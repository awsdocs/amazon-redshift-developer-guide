# CURRENT\_DATE function<a name="r_CURRENT_DATE_function"></a>

CURRENT\_DATE returns a date in the current session time zone \(UTC by default\) in the default format: YYYY\-MM\-DD\.

**Note**  
CURRENT\_DATE returns the start date for the current transaction, not for the start of the current statement\. Consider the scenario where you start a transaction containing multiple statements on 10/01/08 23:59, and the statement containing CURRENT\_DATE runs at 10/02/08 00:00\. CURRENT\_DATE returns `10/01/08`, not `10/02/08`\.

## Syntax<a name="r_CURRENT_DATE_function-syntax"></a>

```
CURRENT_DATE
```

## Return type<a name="r_CURRENT_DATE_function-return-type"></a>

DATE

## Examples<a name="r_CURRENT_DATE_function-examples"></a>

The following example returns the current date\. 

```
select current_date;
   date
------------
2008-10-01
(1 row)
```