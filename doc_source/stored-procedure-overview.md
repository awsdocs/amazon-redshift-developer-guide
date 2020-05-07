# Creating stored procedures in Amazon Redshift<a name="stored-procedure-overview"></a>

You can define an Amazon Redshift stored procedure using the PostgreSQL procedural language PL/pgSQL to perform a set of SQL queries and logical operations\. The procedure is stored in the database and is available for any user with sufficient privileges to run\.  

Unlike a user\-defined function \(UDF\), a stored procedure can incorporate data definition language \(DDL\) and data manipulation language \(DML\) in addition to SELECT queries\. A stored procedure doesn't need to return a value\. You can use procedural language, including looping and conditional expressions, to control logical flow\. 

For details about SQL commands to create and manage stored procedures, see the following command topics:
+ [CREATE PROCEDURE](r_CREATE_PROCEDURE.md)
+ [ALTER PROCEDURE](r_ALTER_PROCEDURE.md)
+ [DROP PROCEDURE](r_DROP_PROCEDURE.md)
+ [SHOW PROCEDURE](r_SHOW_PROCEDURE.md)
+ [CALL](r_CALL_procedure.md)
+ [GRANT](r_GRANT.md)
+ [REVOKE](r_REVOKE.md)
+ [ALTER DEFAULT PRIVILEGES](r_ALTER_DEFAULT_PRIVILEGES.md)

**Topics**
+ [Overview of stored procedures in Amazon Redshift](stored-procedure-create.md)
+ [PL/pgSQL language reference](c_pl_pgSQL_reference.md)