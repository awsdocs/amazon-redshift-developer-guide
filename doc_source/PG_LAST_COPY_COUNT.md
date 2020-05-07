# PG\_LAST\_COPY\_COUNT<a name="PG_LAST_COPY_COUNT"></a>

Returns the number of rows that were loaded by the last COPY command executed in the current session\. PG\_LAST\_COPY\_COUNT is updated with the last COPY ID, which is the query ID of the last COPY that began the load process, even if the load failed\. The query ID and COPY ID are updated when the COPY command begins the load process\. 

If the COPY fails because of a syntax error or because of insufficient privileges, the COPY ID is not updated and PG\_LAST\_COPY\_COUNT returns the count for the previous COPY\. If no COPY commands were executed in the current session, or if the last COPY failed during loading, PG\_LAST\_COPY\_COUNT returns 0\. For more information, see [PG\_LAST\_COPY\_ID](PG_LAST_COPY_ID.md)\. 

## Syntax<a name="PG_LAST_COPY_COUNT-synopsis"></a>

```
pg_last_copy_count()
```

## Return type<a name="PG_LAST_COPY_COUNT-return-type"></a>

Returns BIGINT\.

## Example<a name="PG_LAST_COPY_COUNT-example"></a>

The following query returns the number of rows loaded by the latest COPY command in the current session\.

```
select pg_last_copy_count();

pg_last_copy_count
--------------------
             192497
(1 row)
```