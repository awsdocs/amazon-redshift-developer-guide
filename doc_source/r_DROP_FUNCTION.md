# DROP FUNCTION<a name="r_DROP_FUNCTION"></a>

Removes a user\-defined function \(UDF\) from the database\. The function's signature, or list of argument data types, must be specified because multiple functions can exist with the same name but different signatures\. You can't drop an Amazon Redshift built\-in function\.

This command isn't reversible\.

## Syntax<a name="r_DROP_FUNCTION-synopsis"></a>

```
DROP FUNCTION name 
( [arg_name] arg_type   [, ...] )
[ CASCADE | RESTRICT ]
```

## Parameters<a name="r_DROP_FUNCTION-parameters"></a>

 *name*   
The name of the function to be removed\.

 *arg\_name*   
The name of an input argument\. DROP FUNCTION ignores argument names, because only the argument data types are needed to determine the function's identity\.

 *arg\_type*   
The data type of the input argument\. You can supply a comma\-separated list with a maximum of 32 data types\.

 CASCADE   
Keyword specifying to automatically drop objects that depend on the function, such as views\.   
To create a view that isn't dependent on a function, include the WITH NO SCHEMA BINDING clause in the view definition\. For more information, see [CREATE VIEW](r_CREATE_VIEW.md)\.

 RESTRICT   
Keyword specifying that if any objects depend on depend on the function, do not drop the function and return a message\. This action is the default\.

## Examples<a name="r_DROP_FUNCTION-examples"></a>

The following example drops the function named `f_sqrt`:

```
drop function f_sqrt(int);
```

To remove a function that has dependencies, use the CASCADE option, as shown in the following example:

```
drop function f_sqrt(int)cascade;
```