# EXECUTE<a name="r_EXECUTE"></a>

Executes a previously prepared statement\. 

## Syntax<a name="r_EXECUTE-synopsis"></a>

```
EXECUTE plan_name [ (parameter [, ...]) ]
```

## Parameters<a name="r_EXECUTE-parameters"></a>

 *plan\_name*   
Name of the prepared statement to be executed\. 

 *parameter*   
The actual value of a parameter to the prepared statement\. This must be an expression yielding a value of a type compatible with the data type specified for this parameter position in the PREPARE command that created the prepared statement\. 

## Usage notes<a name="r_EXECUTE_usage_notes"></a>

EXECUTE is used to execute a previously prepared statement\. Since prepared statements only exist for the duration of a session, the prepared statement must have been created by a PREPARE statement executed earlier in the current session\. 

If the previous PREPARE statement specified some parameters, a compatible set of parameters must be passed to the EXECUTE statement, or else Amazon Redshift returns an error\. Unlike functions, prepared statements aren't overloaded based on the type or number of specified parameters; the name of a prepared statement must be unique within a database session\. 

When an EXECUTE command is issued for the prepared statement, Amazon Redshift may optionally revise the query execution plan \(to improve performance based on the specified parameter values\) before executing the prepared statement\. Also, for each new execution of a prepared statement, Amazon Redshift may revise the query execution plan again based on the different parameter values specified with the EXECUTE statement\. To examine the query execution plan that Amazon Redshift has chosen for any given EXECUTE statements, use the [EXPLAIN](r_EXPLAIN.md) command\. 

For examples and more information on the creation and usage of prepared statements, see [PREPARE](r_PREPARE.md)\. 

## See also<a name="r_EXECUTE-see-also"></a>

 [DEALLOCATE](r_DEALLOCATE.md), [PREPARE](r_PREPARE.md) 