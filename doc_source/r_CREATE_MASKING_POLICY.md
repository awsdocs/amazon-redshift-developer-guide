# CREATE MASKING POLICY<a name="r_CREATE_MASKING_POLICY"></a>

Creates a new dynamic data masking policy to obfuscate data of a given format\. For more information on dynamic data masking, see [Dynamic data masking \(preview\)](t_ddm.md)\.

Superusers and users or roles that have the sys:secadmin role can create a masking policy\.

## Syntax<a name="r_CREATE_MASKING_POLICY-synopsis"></a>

```
CREATE MASKING POLICY [IF NOT EXISTS]
   policy_name
   WITH (input_columns)
   USING (masking_expression);
```

## Parameters<a name="r_CREATE_MASKING_POLICY-parameters"></a>

 *masking\_policy\_name*   
The name of the masking policy\. The masking policy can't have the same name as another masking policy that already exists in the database\.

*input\_columns*   
A tuple of column names in the format \(col1 datatype, col2 datatype \.\.\.\)\.  
Column names are used as the input for the masking expression\. Column names don't have to match the names of the columns being masked, but the input and output datatypes must match\.

*masking\_expression*  
The SQL expression used to transform the target columns\. It can be written using data manipulation functions such as String manipulation functions, or in conjunction with user\-defined functions written in SQL, Python, or with AWS Lambda\. You can include a tuple of column expressions for masking policies that have multiple outputs\.  
 You must have the USAGE permission on any user\-defined functions that you use in the masking expression\. 