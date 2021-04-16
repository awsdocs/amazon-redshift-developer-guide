# Date and time functions<a name="Date_functions_header"></a>

In this section, you can find information about the date and time scalar functions that Amazon Redshift supports\.

**Topics**
+ [Summary of date and time functions](#date-functions-summary)
+ [Date and time functions in transactions](#date-functions-transactions)
+ [Deprecated leader node\-only functions](#date-functions-deprecated)
+ [\+ \(Concatenation\) operator](r_DATE-CONCATENATE_function.md)
+ [ADD\_MONTHS function](r_ADD_MONTHS.md)
+ [AT TIME ZONE function](r_AT_TIME_ZONE.md)
+ [CONVERT\_TIMEZONE function](CONVERT_TIMEZONE.md)
+ [CURRENT\_DATE function](r_CURRENT_DATE_function.md)
+ [DATE\_CMP function](r_DATE_CMP.md)
+ [DATE\_CMP\_TIMESTAMP function](r_DATE_CMP_TIMESTAMP.md)
+ [DATE\_CMP\_TIMESTAMPTZ function](r_DATE_CMP_TIMESTAMPTZ.md)
+ [DATE\_PART\_YEAR function](r_DATE_PART_YEAR.md)
+ [DATEADD function](r_DATEADD_function.md)
+ [DATEDIFF function](r_DATEDIFF_function.md)
+ [DATE\_PART function](r_DATE_PART_function.md)
+ [DATE\_TRUNC function](r_DATE_TRUNC.md)
+ [EXTRACT function](r_EXTRACT_function.md)
+ [GETDATE function](r_GETDATE.md)
+ [INTERVAL\_CMP function](r_INTERVAL_CMP.md)
+ [LAST\_DAY function](r_LAST_DAY.md)
+ [MONTHS\_BETWEEN function](r_MONTHS_BETWEEN_function.md)
+ [NEXT\_DAY function](r_NEXT_DAY.md)
+ [SYSDATE function](r_SYSDATE.md)
+ [TIMEOFDAY function](r_TIMEOFDAY_function.md)
+ [TIMESTAMP\_CMP function](r_TIMESTAMP_CMP.md)
+ [TIMESTAMP\_CMP\_DATE function](r_TIMESTAMP_CMP_DATE.md)
+ [TIMESTAMP\_CMP\_TIMESTAMPTZ function](r_TIMESTAMP_CMP_TIMESTAMPTZ.md)
+ [TIMESTAMPTZ\_CMP function](r_TIMESTAMPTZ_CMP.md)
+ [TIMESTAMPTZ\_CMP\_DATE function](r_TIMESTAMPTZ_CMP_DATE.md)
+ [TIMESTAMPTZ\_CMP\_TIMESTAMP function](r_TIMESTAMPTZ_CMP_TIMESTAMP.md)
+ [TIMEZONE function](r_TIMEZONE.md)
+ [TO\_TIMESTAMP function](r_TO_TIMESTAMP.md)
+ [TRUNC Date function](r_TRUNC_date.md)
+ [Date parts for date or timestamp functions](r_Dateparts_for_datetime_functions.md)

## Summary of date and time functions<a name="date-functions-summary"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/Date_functions_header.html)

**Note**  
Leap seconds are not considered in elapsed\-time calculations\.

## Date and time functions in transactions<a name="date-functions-transactions"></a>

When you execute the following functions within a transaction block \(BEGIN … END\), the function returns the start date or time of the current transaction, not the start of the current statement\.
+ SYSDATE
+ TIMESTAMP
+ CURRENT\_DATE

The following functions always return the start date or time of the current statement, even when they are within a transaction block\.
+ GETDATE
+ TIMEOFDAY

## Deprecated leader node\-only functions<a name="date-functions-deprecated"></a>

The following date functions are deprecated because they execute only on the leader node\. For more information, see [Leader node–only functions](c_SQL_functions_leader_node_only.md)\.
+ AGE\. Use [DATEDIFF function](r_DATEDIFF_function.md) instead\.
+ CURRENT\_TIME\. Use [GETDATE function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\. 
+ CURRENT\_TIMESTAMP\. Use [GETDATE function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\.
+ LOCALTIME\. Use [GETDATE function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\.
+ LOCALTIMESTAMP\. Use [GETDATE function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\.
+ ISFINITE 
+ NOW\. Use [GETDATE function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\.