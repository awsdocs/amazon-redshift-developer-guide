# PG\_LAST\_UNLOAD\_COUNT<a name="PG_LAST_UNLOAD_COUNT"></a>

Returns the number of rows that were unloaded by the last UNLOAD command executed in the current session\. PG\_LAST\_UNLOAD\_COUNT is updated with the query ID of the last UNLOAD, even if the operation failed\. The query ID is updated when the UNLOAD is executed\. If the UNLOAD fails because of a syntax error or because of insufficient privileges, PG\_LAST\_UNLOAD\_COUNT returns the count for the previous UNLOAD\. If no UNLOAD commands were executed in the current session, or if the last UNLOAD failed during the unload operation, PG\_LAST\_UNLOAD\_COUNT returns 0\. 

## Syntax<a name="PG_LAST_UNLOAD_COUNT-synopsis"></a>

```
pg_last_unload_count()
```

## Return type<a name="PG_LAST_UNLOAD_COUNT-return-type"></a>

Returns BIGINT\.

## Example<a name="PG_LAST_UNLOAD_COUNT-example"></a>

The following query returns the number of rows unloaded by the latest UNLOAD command in the current session\.

```
select pg_last_unload_count();

pg_last_unload_count
--------------------
             192497
(1 row)
```