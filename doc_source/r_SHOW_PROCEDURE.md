# SHOW PROCEDURE<a name="r_SHOW_PROCEDURE"></a>

Shows the definition of a given stored procedure, including its signature\. You can use the output of a SHOW PROCEDURE to recreate the stored procedure\. 

## Syntax<a name="r_SHOW_PROCEDURE-synopsis"></a>

```
SHOW PROCEDURE sp_name [( [ [ argname ] [ argmode ] argtype [, ...] ] )]
```

## Parameters<a name="r_SHOW_PROCEDURE-parameters"></a>

 *sp\_name*   
The name of the procedure to show\. 

*\[argname\] \[ argmode\] argtype*   
Input argument types to identify the stored procedure\. Optionally, you can include the full argument data types, including OUT arguments\. This part is optional if the name of the stored procedure is unique \(that is, not overloaded\)\.

## Examples<a name="r_SHOW_PROCEDURE-examples"></a>

The following example shows the definition of the procedure `test_spl2`\.

```
show procedure test_sp2(int, varchar);
                                        Stored Procedure Definition
------------------------------------------------------------------------------------------------------------
CREATE OR REPLACE PROCEDURE public.test_sp2(f1 integer, INOUT f2 character varying, OUT character varying)
LANGUAGE plpgsql
AS $_$
DECLARE
out_var alias for $3;
loop_var int;
BEGIN
IF f1 is null OR f2 is null THEN
RAISE EXCEPTION 'input cannot be null';
END IF;
CREATE TEMP TABLE etl(a int, b varchar);
FOR loop_var IN 1..f1 LOOP
insert into etl values (loop_var, f2);
f2 := f2 || '+' || f2;
END LOOP;
SELECT INTO out_var count(*) from etl;
END;
$_$

(1 row)
```