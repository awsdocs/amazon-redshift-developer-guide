# CALL<a name="r_CALL_procedure"></a>

Runs a stored procedure\. The CALL command must include the procedure name and the input argument values\. You must call a stored procedure by using the CALL statement\. CALL can't be part of any regular queries\. 

## Syntax<a name="r_CALL_procedure-synopsis"></a>

```
CALL sp_name ( [ argument ] [, ...] )
```

## Parameters<a name="r_CALL_procedure-parameters"></a>

 *sp\_name*   
The name of the procedure to run\. 

 *argument*   
The value of the input argument\. This parameter can also be a function name, for example `pg_last_query_id()`\. You can't use queries as CALL arguments\. 

## Usage notes<a name="r_CALL_procedure-usage-notes"></a>

Amazon Redshift stored procedures support nested and recursive calls, as described following\. In addition, make sure your driver support is up\-to\-date, also described following\.

**Topics**
+ [Nested calls](#r_CALL_procedure-nested-calls)
+ [Driver support](#r_CALL_procedure-driver-support)

### Nested calls<a name="r_CALL_procedure-nested-calls"></a>

Amazon Redshift stored procedures support nested and recursive calls\. The maximum number of nesting levels allowed is 16\. Nested calls can encapsulate business logic into smaller procedures, which can be shared by multiple callers\. 

If you call a nested procedure that has output parameters, the inner procedure must define INOUT arguments\. In this case, the inner procedure is passed in a nonconstant variable\. OUT arguments aren't allowed\. This behavior occurs because a variable is needed to hold the output of the inner call\.

The relationship between inner and outer procedures is logged in the `from_sp_call` column of [SVL\_STORED\_PROC\_CALL](r_SVL_STORED_PROC_CALL.md)\. 

The following example shows passing variables to a nested procedure call through INOUT arguments\.

```
CREATE OR REPLACE PROCEDURE inner_proc(INOUT a int, b int, INOUT c int) LANGUAGE plpgsql
AS $$
BEGIN
  a := b * a;
  c := b * c;
END;
$$;

CREATE OR REPLACE PROCEDURE outer_proc(multiplier int) LANGUAGE plpgsql
AS $$
DECLARE
  x int := 3;
  y int := 4;
BEGIN
  DROP TABLE IF EXISTS test_tbl;
  CREATE TEMP TABLE test_tbl(a int, b varchar(256));
  CALL inner_proc(x, multiplier, y);
  insert into test_tbl values (x, y::varchar);
END;
$$;

CALL outer_proc(5);
      
SELECT * from test_tbl;
 a  | b
----+----
 15 | 20
(1 row)
```

### Driver support<a name="r_CALL_procedure-driver-support"></a>

We recommend that you upgrade your Java Database Connectivity \(JDBC\) and Open Database Connectivity \(ODBC\) drivers to the latest version that has support for Amazon Redshift stored procedures\. 

You might be able to use your existing driver if your client tool uses driver API operations that pass through the CALL statement to the server\. Output parameters, if any, are returned as a result set of one row\. 

The latest versions of Amazon Redshift JDBC and ODBC drivers have metadata support for stored procedure discovery\. They also have `CallableStatement` support for custom Java applications\. For more information on drivers, see [Connecting to an Amazon Redshift Cluster Using SQL Client Tools](https://docs.aws.amazon.com/redshift/latest/mgmt/connecting-to-cluster.html) in the *Amazon Redshift Cluster Management Guide\.* 

**Important**  
Currently, you can't use a `refcursor` data type in a stored procedure using a JDBC or ODBC driver\.

The following examples show how to use different API operations of the JDBC driver for stored procedure calls\.

```
void statement_example(Connection conn) throws SQLException {
  statement.execute("CALL sp_statement_example(1)");
}

void prepared_statement_example(Connection conn) throws SQLException {
  String sql = "CALL sp_prepared_statement_example(42, 84)";
  PreparedStatement pstmt = conn.prepareStatement(sql);
  pstmt.execute();
}

void callable_statement_example(Connection conn) throws SQLException {
  CallableStatement cstmt = conn.prepareCall("CALL sp_create_out_in(?,?)");
  cstmt.registerOutParameter(1, java.sql.Types.INTEGER);  
  cstmt.setInt(2, 42);
  cstmt.executeQuery();
  Integer out_value = cstmt.getInt(1);
}
```

## Examples<a name="r_CALL_procedure-examples"></a>

The following example calls the procedure name `test_spl`\.

```
call test_sp1(3,'book');
INFO:  Table "tmp_tbl" does not exist and will be skipped
INFO:  min_val = 3, f2 = book
```

The following example calls the procedure name `test_spl2`\.

```
call test_sp2(2,'2019');

         f2          | column2
---------------------+---------
 2019+2019+2019+2019 | 2
(1 row)
```