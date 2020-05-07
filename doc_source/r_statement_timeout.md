# statement\_timeout<a name="r_statement_timeout"></a>

## Values \(default in bold\)<a name="r_statement_timeout-values"></a>

 **0 \(turns off limitation\)**, x milliseconds

## Description<a name="r_statement_timeout-description"></a>

Aborts any statement that takes over the specified number of milliseconds\.

The statement\_timeout value is the maximum amount of time a query can run before Amazon Redshift terminates it\. This time includes planning, queueing in WLM, and execution time\. Compare this time to WLM timeout \(max\_execution\_time\) and a QMR \(query\_execution\_time\), which include only execution time\.

If WLM timeout \(max\_execution\_time\) is also specified as part of a WLM configuration, the lower of statement\_timeout and max\_execution\_time is used\. For more information, see [WLM timeout](cm-c-defining-query-queues.md#wlm-timeout)\.

## Example<a name="r_statement_timeout-example"></a>

Because the following query takes longer than 1 millisecond, it times out and is cancelled\.

```
set statement_timeout to 1;

select * from listing where listid>5000;
ERROR:  Query (150) cancelled on user's request
```