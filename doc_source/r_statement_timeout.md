# statement\_timeout<a name="r_statement_timeout"></a>

## Values \(Default in Bold\)<a name="r_statement_timeout-values"></a>

 **0 \(turns off limitation\)**, x milliseconds

## Description<a name="r_statement_timeout-description"></a>

Aborts any statement that takes over the specified number of milliseconds\.

If WLM timeout \(max\_execution\_time\) is also specified as part of a WLM configuration, the lower of statement\_timeout and max\_execution\_time is used\. For more information, see [WLM Timeout](cm-c-defining-query-queues.md#wlm-timeout)\.

## Example<a name="r_statement_timeout-example"></a>

Because the following query takes longer than 1 millisecond, it times out and is cancelled\.

```
set statement_timeout to 1;

select * from listing where listid>5000;
ERROR:  Query (150) cancelled on user's request
```