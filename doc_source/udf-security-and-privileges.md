# UDF security and privileges<a name="udf-security-and-privileges"></a>

To create a UDF, you must have permission for usage on language for SQL or plpythonu \(Python\)\. By default, USAGE ON LANGUAGE SQL is granted to PUBLIC, but you must explicitly grant USAGE ON LANGUAGE PLPYTHONU to specific users or groups\. 

To revoke usage for SQL, first revoke usage from PUBLIC\. Then grant usage on SQL only to the specific users or groups permitted to create SQL UDFs\. The following example revokes usage on SQL from PUBLIC\. Then it grants usage to the user group `udf_devs`\.

```
revoke usage on language sql from PUBLIC;
grant usage on language sql to group udf_devs;
```

To execute a UDF, you must have execute permission for each function\. By default, execute permission for new UDFs is granted to PUBLIC\. To restrict usage, revoke execute from PUBLIC for the function\. Then grant the privilege to specific individuals or groups\. 

The following example revokes execution on function `f_py_greater` from PUBLIC\. Then it grants usage to the user group `udf_devs`\.

```
revoke execute on function f_py_greater(a float, b float) from PUBLIC;
grant execute on function f_py_greater(a float, b float) to group udf_devs;
```

Superusers have all privileges by default\. 

For more information, see [GRANT](r_GRANT.md) and [REVOKE](r_REVOKE.md)\.