# PG\_LAST\_COPY\_ID<a name="PG_LAST_COPY_ID"></a>

Returns the query ID of the most recently executed COPY command in the current session\. If no COPY commands have been executed in the current session, PG\_LAST\_COPY\_ID returns \-1\. 

 The value for PG\_LAST\_COPY\_ID is updated when the COPY command begins the load process\. If the COPY fails because of invalid load data, the COPY ID is updated, so you can use PG\_LAST\_COPY\_ID when you query STL\_LOAD\_ERRORS table\. If the COPY transaction is rolled back, the COPY ID is not updated\. 

The COPY ID is not updated if the COPY command fails because of an error that occurs before the load process begins, such as a syntax error, access error, invalid credentials, or insufficient privileges\. The COPY ID is not updated if the COPY fails during the analyze compression step, which begins after a successful connection, but before the data load\. COPY performs compression analysis when the COMPUPDATE parameter is set to ON or when the target table is empty and all the table columns either have RAW encoding or no encoding\. 

## Syntax<a name="PG_LAST_COPY_ID-synopsis"></a>

```
pg_last_copy_id()
```

## Return type<a name="PG_LAST_COPY_ID-return-type"></a>

Returns an integer\.

## Example<a name="PG_LAST_COPY_ID-example"></a>

The following query returns the query ID of the latest COPY command in the current session\.

```
select pg_last_copy_id();

pg_last_copy_id
---------------
          5437
(1 row)
```

The following query joins STL\_LOAD\_ERRORS to STL\_LOADERROR\_DETAIL to view the details errors that occurred during the most recent load in the current session:

```
select d.query, substring(d.filename,14,20), 
d.line_number as line, 
substring(d.value,1,16) as value,
substring(le.err_reason,1,48) as err_reason
from stl_loaderror_detail d, stl_load_errors le
where d.query = le.query
and d.query = pg_last_copy_id(); 

 query |    substring      | line |  value   |              err_reason
-------+-------------------+------+----------+----------------------------
    558| allusers_pipe.txt |  251 | 251      | String contains invalid or 
                                               unsupported UTF8 code
    558| allusers_pipe.txt |  251 | ZRU29FGR | String contains invalid or 
                                               unsupported UTF8 code
    558| allusers_pipe.txt |  251 | Kaitlin  | String contains invalid or 
                                               unsupported UTF8 code
    558| allusers_pipe.txt |  251 | Walter   | String contains invalid or 
                                               unsupported UTF8 code
```