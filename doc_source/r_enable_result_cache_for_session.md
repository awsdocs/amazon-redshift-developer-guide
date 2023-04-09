# enable\_result\_cache\_for\_session<a name="r_enable_result_cache_for_session"></a>

## Values \(default in bold\)<a name="r_enable_result_cache_for_session-values"></a>

**on \(true\)**, off \(false\)

## Description<a name="r_enable_result_cache_for_session-description"></a>

Specifies whether to use query results caching\. If `enable_result_cache_for_session` is `on`, Amazon Redshift checks for a valid, cached copy of the query results when a query is submitted\. If a match is found in the result cache, Amazon Redshift uses the cached results and doesn’t run the query\. If `enable_result_cache_for_session` is `off`, Amazon Redshift ignores the results cache and runs all queries when they are submitted\. 

## Example<a name="r_enable_result_cache_for_session-example"></a>

```
SET enable_result_cache_for_session TO false;
--Amazon Redshift now ignores the results cache
```