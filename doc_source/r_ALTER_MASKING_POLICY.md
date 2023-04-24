# ALTER MASKING POLICY<a name="r_ALTER_MASKING_POLICY"></a>

Alters an existing dynamic data masking policy\. For more information on dynamic data masking, see [Dynamic data masking](t_ddm.md)\.

Superusers and users or roles that have the sys:secadmin role can alter a masking policy\.

## Syntax<a name="r_ALTER_MASKING_POLICY-synopsis"></a>

```
ALTER MASKING POLICY policy_name 
   USING (masking_expression);
```

## Parameters<a name="r_ALTER_MASKING_POLICY-parameters"></a>

*policy\_name*   
 The name of the masking policy\. This must be the name of a masking policy that already exists in the database\. 

*masking\_expression*  
The SQL expression used to transform the target columns\. It can be written using data manipulation functions such as String manipulation functions, or in conjunction with user\-defined functions written in SQL, Python, or with AWS Lambda\.   
 The expression must match the original expression's input columns and data types\. For example, if the original masking policy's input columns were `sample_1 FLOAT` and `sample_2 VARCHAR(10)`, you wouldn't be able to alter the masking policy to take a third column, or make the policy take a FLOAT and a BOOLEAN\. If you use a constant as your masking expression, you must explicitly cast it to a type that matches the input type\.  
 You must have the USAGE permission on any user\-defined functions that you use in the masking expression\. 