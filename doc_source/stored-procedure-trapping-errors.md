# Trapping errors<a name="stored-procedure-trapping-errors"></a>

An error encountered during the execution of a stored procedure ends the execution flow and ends the transaction\. You can trap errors using an EXCEPTION block\. The only supported condition is OTHERS, which matches every error type except query cancellation\. 

```
[ <<label>> ]
[ DECLARE
  declarations ]
BEGIN
  statements
EXCEPTION
  WHEN OTHERS THEN
    handler_statements
END;
```

In an Amazon Redshift stored procedure, the only supported *handler\_statement* is RAISE\. Any error encountered during the execution automatically ends the entire stored procedure call and rolls back the transaction\. This occurs because subtransactions are not supported\.

If an error occurs in the exception handling block, it is propagated out and can be caught by an outer exception handling block, if one exists\. 