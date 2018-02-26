# DROP LIBRARY<a name="r_DROP_LIBRARY"></a>

Removes a custom Python library from the database\. Only the library owner or a superuser can drop a library\. DROP LIBRARY cannot be run inside a transaction block \(BEGIN … END\)\. For more information, see [CREATE LIBRARY](r_CREATE_LIBRARY.md)\.

This command is not reversible\. The DROP LIBRARY command commits immediately\. If a UDF that depends on the library is running concurrently, the UDF might fail, even if the UDF is running within a transaction\.

## Syntax<a name="r_DROP_LIBRARY-synopsis"></a>

```
DROP LIBRARY library_name
```

## Parameters<a name="r_DROP_LIBRARY-parameters"></a>

 *library\_name*   
The name of the library\.