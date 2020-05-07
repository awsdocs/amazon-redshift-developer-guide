# PG\_LAST\_UNLOAD\_ID<a name="PG_LAST_UNLOAD_ID"></a>

Returns the query ID of the most recently executed UNLOAD command in the current session\. If no UNLOAD commands have been executed in the current session, PG\_LAST\_UNLOAD\_ID returns \-1\. 

 The value for PG\_LAST\_UNLOAD\_ID is updated when the UNLOAD command begins the load process\. If the UNLOAD fails because of invalid load data, the UNLOAD ID is updated, so you can use the UNLOAD ID for further investigation\. If the UNLOAD transaction is rolled back, the UNLOAD ID is not updated\. 

The UNLOAD ID is not updated if the UNLOAD command fails because of an error that occurs before the load process begins, such as a syntax error, access error, invalid credentials, or insufficient privileges\. 

## Syntax<a name="PG_LAST_UNLOAD_ID-synopsis"></a>

```
PG_LAST_UNLOAD_ID()
```

## Return type<a name="PG_LAST_UNLOAD_ID-return-type"></a>

Returns an integer\.

## Example<a name="PG_LAST_UNLOAD_ID-example"></a>

The following query returns the query ID of the latest UNLOAD command in the current session\.

```
select PG_LAST_UNLOAD_ID();

PG_LAST_UNLOAD_ID
---------------
          5437
(1 row)
```