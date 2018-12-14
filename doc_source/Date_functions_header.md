# Date and Time Functions<a name="Date_functions_header"></a>

In this section, you can find information about the date and time scalar functions that Amazon Redshift supports\.

**Topics**
+ [Summary of Date and Time Functions](#date-functions-summary)
+ [Date and Time Functions in Transactions](#date-functions-transactions)
+ [Deprecated Leader Node\-Only Functions](#date-functions-deprecated)
+ [ADD\_MONTHS Function](r_ADD_MONTHS.md)
+ [AT TIME ZONE Function](r_AT_TIME_ZONE.md)
+ [CONVERT\_TIMEZONE Function](CONVERT_TIMEZONE.md)
+ [CURRENT\_DATE Function](r_CURRENT_DATE_function.md)
+ [DATE\_CMP Function](r_DATE_CMP.md)
+ [DATE\_CMP\_TIMESTAMP Function](r_DATE_CMP_TIMESTAMP.md)
+ [DATE\_CMP\_TIMESTAMPTZ Function](r_DATE_CMP_TIMESTAMPTZ.md)
+ [DATE\_PART\_YEAR Function](r_DATE_PART_YEAR.md)
+ [DATEADD Function](r_DATEADD_function.md)
+ [DATEDIFF Function](r_DATEDIFF_function.md)
+ [DATE\_PART Function](r_DATE_PART_function.md)
+ [DATE\_TRUNC Function](r_DATE_TRUNC.md)
+ [EXTRACT Function](r_EXTRACT_function.md)
+ [GETDATE Function](r_GETDATE.md)
+ [INTERVAL\_CMP Function](r_INTERVAL_CMP.md)
+ [LAST\_DAY Function](r_LAST_DAY.md)
+ [MONTHS\_BETWEEN Function](r_MONTHS_BETWEEN_function.md)
+ [NEXT\_DAY Function](r_NEXT_DAY.md)
+ [SYSDATE Function](r_SYSDATE.md)
+ [TIMEOFDAY Function](r_TIMEOFDAY_function.md)
+ [TIMESTAMP\_CMP Function](r_TIMESTAMP_CMP.md)
+ [TIMESTAMP\_CMP\_DATE Function](r_TIMESTAMP_CMP_DATE.md)
+ [TIMESTAMP\_CMP\_TIMESTAMPTZ Function](r_TIMESTAMP_CMP_TIMESTAMPTZ.md)
+ [TIMESTAMPTZ\_CMP Function](r_TIMESTAMPTZ_CMP.md)
+ [TIMESTAMPTZ\_CMP\_DATE Function](r_TIMESTAMPTZ_CMP_DATE.md)
+ [TIMESTAMPTZ\_CMP\_TIMESTAMP Function](r_TIMESTAMPTZ_CMP_TIMESTAMP.md)
+ [TIMEZONE Function](r_TIMEZONE.md)
+ [TO\_TIMESTAMP Function](r_TO_TIMESTAMP.md)
+ [TRUNC Date Function](r_TRUNC_date.md)
+ [Dateparts for Date or Time Stamp Functions](r_Dateparts_for_datetime_functions.md)

## Summary of Date and Time Functions<a name="date-functions-summary"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/Date_functions_header.html)

**Note**  
Leap seconds are not considered in elapsed\-time calculations\.

## Date and Time Functions in Transactions<a name="date-functions-transactions"></a>

When you execute the following functions within a transaction block \(BEGIN … END\), the function returns the start date or time of the current transaction, not the start of the current statement\.
+ SYSDATE
+ TIMESTAMP
+ CURRENT\_DATE

The following functions always return the start date or time of the current statement, even when they are within a transaction block\.
+ GETDATE
+ TIMEOFDAY

## Deprecated Leader Node\-Only Functions<a name="date-functions-deprecated"></a>

The following date functions are deprecated because they execute only on the leader node\. For more information, see [Leader Node–Only Functions](c_SQL_functions_leader_node_only.md)\.
+ AGE\. Use [DATEDIFF Function](r_DATEDIFF_function.md) instead\.
+ CURRENT\_TIME\. Use [GETDATE Function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\. 
+ CURRENT\_TIMESTAMP\. Use [GETDATE Function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\.
+ LOCALTIME\. Use [GETDATE Function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\.
+ LOCALTIMESTAMP\. Use [GETDATE Function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\.
+ ISFINITE 
+ NOW\. Use [GETDATE Function](r_GETDATE.md) or [SYSDATE](r_SYSDATE.md) instead\.