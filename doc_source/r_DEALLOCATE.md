# DEALLOCATE<a name="r_DEALLOCATE"></a>

Deallocates a prepared statement\. 

## Syntax<a name="r_DEALLOCATE-synopsis"></a>

```
DEALLOCATE [PREPARE] plan_name
```

## Parameters<a name="r_DEALLOCATE-parameters"></a>

PREPARE   
This keyword is optional and is ignored\. 

 *plan\_name*   
The name of the prepared statement to deallocate\. 

## Usage Notes<a name="r_DEALLOCATE_usage_notes"></a>

DEALLOCATE is used to deallocate a previously prepared SQL statement\. If you don't explicitly deallocate a prepared statement, it is deallocated when the current session ends\. For more information on prepared statements, see [PREPARE](r_PREPARE.md)\.

## See Also<a name="r_DEALLOCATE-see-also"></a>

 [EXECUTE](r_EXECUTE.md), [PREPARE](r_PREPARE.md) 