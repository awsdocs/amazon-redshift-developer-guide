# Returning a result set<a name="stored-procedure-result-set"></a>

You can return a result set using a cursor or a temp table\.

## Returning a cursor<a name="stored-procedure-return-cursor"></a>

To return a cursor, create a procedure with an INOUT argument defined with a `refcursor` data type\. When you call the procedure, give the cursor a name, then you can fetch the results from the cursor by name\.

The following example creates a procedure named `get_result_set` with an INOUT argument named `rs_out` using the `refcursor` data type\. The procedure opens the cursor using a SELECT statement\.

```
CREATE OR REPLACE PROCEDURE get_result_set (param IN integer, rs_out INOUT refcursor)
AS $$
BEGIN
  OPEN rs_out FOR SELECT * FROM fact_tbl where id >= param;
END;
$$ LANGUAGE plpgsql;
```

The following CALL command opens the cursor with the name `mycursor`\. Use cursors only within transactions\. 

```
BEGIN;
CALL get_result_set(1, 'mycursor');
```

After the cursor is opened, you can fetch from the cursor, as the following example shows\.

```
FETCH ALL FROM mycursor;

    id | secondary_id | name
-------+--------------+---------
     1 |            1 | Joe
     1 |            2 | Ed
     2 |            1 | Mary
     1 |            3 | Mike
(4 rows)
```

In the end, the transaction is either committed or rolled back\.

```
COMMIT;   
```

A cursor returned by a stored procedure is subject to the same constraints and performance considerations as described in DECLARE CURSOR\. For more information, see [Cursor constraints](declare.md#declare-constraints)\.

The following example shows the calling of the `get_result_set` stored procedure using a `refcursor` data type from JDBC\. The literal `'mycursor'` \(the name of the cursor\) is passed to the `prepareStatement`\. Then the results are fetched from the `ResultSet`\.

```
static void refcursor_example(Connection conn) throws SQLException {
    conn.setAutoCommit(false);
    PreparedStatement proc = conn.prepareStatement("CALL get_result_set(1, 'mycursor')");
    proc.execute();
    ResultSet rs = statement.executeQuery("fetch all from mycursor");
    while (rs.next()) {
      int n = rs.getInt(1);
      System.out.println("n " + n);
    }
```

## Using a temp table<a name="stored-procedure-return-cursor"></a>

To return results, you can return a handle to a temp table containing result rows\. The client can supply a name as a parameter to the stored procedure\. Inside the stored procedure, dynamic SQL can be used to operate on the temp table\. The following shows an example\.

```
CREATE PROCEDURE get_result_set(param IN integer, tmp_name INOUT varchar(256)) as $$
DECLARE
  row record;
BEGIN
  EXECUTE 'drop table if exists ' || tmp_name;
  EXECUTE 'create temp table ' || tmp_name || ' as select * from fact_tbl where id >= ' || param;
END;
$$ LANGUAGE plpgsql;

CALL get_result_set(2, 'myresult');
 tmp_name
-----------
 myresult
(1 row)

SELECT * from myresult;
 id | secondary_id | name
----+--------------+------
  1 |            1 | Joe
  2 |            1 | Mary
  1 |            2 | Ed
  1 |            3 | Mike
(4 rows)
```