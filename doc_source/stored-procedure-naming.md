# Naming stored procedures<a name="stored-procedure-naming"></a>

If you define a procedure with the same name and different input argument data types, or signature, you create a new procedure\. In other words, the procedure name is overloaded\. For more information, see [Overloading procedure names](#stored-procedure-overloading-name)\. Amazon Redshift doesn't enable procedure overloading based on output arguments\. In other words, you can't have two procedures with the same name and input argument data types but different output argument types\.

The owner or a superuser can replace the body of a stored procedure with a new one with the same signature\. To change the signature or return types of a stored procedure, drop the stored procedure and recreate it\. For more information, see [DROP PROCEDURE](r_DROP_PROCEDURE.md) and [CREATE PROCEDURE](r_CREATE_PROCEDURE.md)\.

You can avoid potential conflicts and unexpected results by considering your naming conventions for stored procedures before implementing them\. Because you can overload procedure names, they can collide with existing and future Amazon Redshift procedure names\.

## Overloading procedure names<a name="stored-procedure-overloading-name"></a>

A procedure is identified by its name and signature, which is the number of input arguments and the data types of the arguments\. Two procedures in the same schema can have the same name if they have different signatures\. In other words, you can overload procedure names\.

When you run a procedure, the query engine determines which procedure to call based on the number of arguments that you provide and the data types of the arguments\. You can use overloading to simulate procedures with a variable number of arguments, up to the limit allowed by the CREATE PROCEDURE command\. For more information, see [CREATE PROCEDURE](r_CREATE_PROCEDURE.md)\.

## Preventing naming conflicts<a name="stored-procedure-name-conflicts"></a>

We recommend that you name all procedures using the prefix `sp_`\. Amazon Redshift reserves the `sp_` prefix exclusively for stored procedures\. By prefixing your procedure names with `sp_`, you ensure that your procedure name won't conflict with any existing or future Amazon Redshift procedure names\. 