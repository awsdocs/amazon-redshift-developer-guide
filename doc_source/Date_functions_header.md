# Date and Time Functions<a name="Date_functions_header"></a>

In this section, you can find information about the date and time scalar functions that Amazon Redshift supports\.

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