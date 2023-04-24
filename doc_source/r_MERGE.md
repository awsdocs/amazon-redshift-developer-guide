# MERGE<a name="r_MERGE"></a>

Conditionally merges rows from a source table into a target table\. Traditionally, this can only be achieved by using multiple insert, update or delete statements separately\. For more information on the operations that MERGE lets you combine, see [UPDATE](https://docs.aws.amazon.com/redshift/latest/dg/r_UPDATE.html), [DELETE](https://docs.aws.amazon.com/redshift/latest/dg/r_DELETE.html), and [INSERT](https://docs.aws.amazon.com/redshift/latest/dg/r_INSERT_30.html)\.

## Syntax<a name="r_MERGE-synopsis"></a>

```
MERGE INTO target_table
USING source_table [ [ AS ] alias ] ON match_condition
WHEN MATCHED THEN { UPDATE SET col_name = { expr } [,...] | DELETE }
WHEN NOT MATCHED THEN INSERT [ ( col_name [,...] ) ] VALUES ( { expr } [, ...] )
```

## Parameters<a name="r_MERGE-parameters"></a>

 *target\_table*  
The temporary or permanent table that the MERGE statement merges into\.

 *source\_table*  
The temporary or permanent table supplying the rows to merge into *target\_table*\. *source\_table* can also be a Spectrum table, Postgres federated table, or MySQL federated table\. *source\_table* can't be a view or a subquery\.

 *alias*  
The temporary alternative name for *source\_table*\.  
This parameter is optional\. Preceding *alias* with AS is also optional\.

 *match\_condition*  
Specifies equal predicates between the source table column and target table column that are used to determine whether the rows in *source\_table* can be matched with rows in *target\_table*\. If the condition is met, MERGE runs *matched\_clause* for that row\. Otherwise MERGE runs *not\_matched\_clause* for that row\.

WHEN MATCHED  
 Specifies the action to be executed when the match condition between a source row and a target row evaluates to True\. You can specify either an UPDATE action or a DELETE action\. 

UPDATE  
 Updates the matched row in *target\_table*\. Only values in the *col\_name* you specify are updated\. 

DELETE  
 Deletes the matched row in *target\_table*\. 

WHEN NOT MATCHED  
 Specifies the action to be executed when the match condition is evaluated to False or Unknown\. You can only specify the INSERT insert action for this clause\. 

INSERT  
 Inserts one row into *target\_table*\. The target *col\_name* can be listed in any order\. If you don’t provide any *col\_name* values, the default order is all the table’s columns in their declared order\. 

 *col\_name*  
One or more column names that you want to modify\. Don't include the table name when specifying the target column\.

 *expr*  
The expression defining the new value for *col\_name*\.

## Usage notes<a name="r_MERGE_usage_notes"></a>
+ To run MERGE statements, you must be the owner of both *source\_table* and *target\_table*, or have the SELECT permission for those tables\. Additionally, you must have UPDATE, DELETE, and INSERT permissions for *target\_table* depending on the operations included in your MERGE statement\.
+  *target\_table* can't be a system table, catalog table, or external table\. 
+  *source\_table* and *target\_table* can't be the same table\. 
+  You can't use the WITH clause in a MERGE statement\. 
+  Rows in *target\_table* can't match multiple rows in *source\_table*\. 

  Consider the following example:

  ```
  CREATE TABLE target (id INT, name CHAR(10));
  CREATE TABLE source (id INT, name CHAR(10));
  
  INSERT INTO target VALUES (1, 'Bob'), (2, 'John');
  INSERT INTO source VALUES (1, 'Tony'), (1, 'Alice'), (3, 'Bill');
  
  MERGE INTO target USING source ON target.id = source.id
  WHEN MATCHED THEN UPDATE SET id = source.id, name = source.name
  WHEN NOT MATCHED THEN INSERT VALUES (source.id, source.name);
  ERROR: Found multiple matches to update the same tuple.
  
  MERGE INTO target USING source ON target.id = source.id
  WHEN MATCHED THEN DELETE
  WHEN NOT MATCHED THEN INSERT VALUES (source.id, source.name);
  ERROR: Found multiple matches to update the same tuple.
  ```

  In both MERGE statements, the operation fails because there are multiple rows in the `source` table with an ID value of `1`\.
+  *match\_condition* and *expr* can't partially reference SUPER type columns\. For example, if your SUPER type object is an array or a structure, you can't use individual elements of that column for *match\_condition* or *expr*, but you can use the entire column\. 

  Consider the following example:

  ```
  CREATE TABLE IF NOT EXISTS target (key INT, value SUPER);
  CREATE TABLE IF NOT EXISTS source (key INT, value SUPER);
  
  INSERT INTO target VALUES (1, JSON_PARSE('{"key": 88}'));
  INSERT INTO source VALUES (1, ARRAY(1, 'John')), (2, ARRAY(2, 'Bill'));
  
  MERGE INTO target USING source ON target.key = source.key
  WHEN matched THEN UPDATE SET value = source.value[0]
  WHEN NOT matched THEN INSERT VALUES (source.key, source.value[0]);
  ERROR: Partial reference of SUPER column is not supported in MERGE statement.
  ```

  For more information on the SUPER type, see [ SUPER type](https://docs.aws.amazon.com/redshift/latest/dg/r_SUPER_type.html)\.
+ If *source\_table* is large, defining the join columns from both *target\_table* and *source\_table* as the distribution keys can improve performance\.

## Examples<a name="sub-examples-merge"></a>

The following example creates two tables, then runs a MERGE operation on them, updating matching rows in the target table and inserting rows that don't match\. Then it inserts another value into the source table and runs another MERGE operation, this time deleting matching rows and inserting the new row from the source table\.

First create and populate the source and target tables\.

```
CREATE TABLE target (id INT, name CHAR(10));
CREATE TABLE source (id INT, name CHAR(10));

INSERT INTO target VALUES (101, 'Bob'), (102, 'John'), (103, 'Susan');
INSERT INTO source VALUES (102, 'Tony'), (103, 'Alice'), (104, 'Bill');

SELECT * FROM target;
 id  |    name
-----+------------
 101 | Bob
 102 | John
 103 | Susan
(3 rows)

SELECT * FROM source;
 id  |    name
-----+------------
 102 | Tony
 103 | Alice
 104 | Bill
(3 rows)
```

Next, merge the source table into the target table, updating the target table with matching rows and insert rows from the source table that have no match\.

```
MERGE INTO target USING source ON target.id = source.id
WHEN MATCHED THEN UPDATE SET id = source.id, name = source.name
WHEN NOT MATCHED THEN INSERT VALUES (source.id, source.name);

SELECT * FROM target;
 id  |    name
-----+------------
 101 | Bob
 102 | Tony
 103 | Alice
 104 | Bill
(4 rows)
```

Note that the rows with id values of 102 and 103 are updated to match the name values from the target table\. Also, a new row with an id value of 104 and name value of Bill is inserted into the target table\.

Next, insert a new row into the source table\.

```
INSERT INTO source VALUES (105, 'David');

SELECT * FROM source;
 id  |    name
-----+------------
 102 | Tony
 103 | Alice
 104 | Bill
 105 | David
(4 rows)
```

Finally, run a merge operation deleting matching rows in the target table, and inserting rows that don't match\.

```
MERGE INTO target USING source ON target.id = source.id
WHEN MATCHED THEN DELETE
WHEN NOT MATCHED THEN INSERT VALUES (source.id, source.name);

SELECT * FROM target;
 id  |    name
-----+------------
 101 | Bob
 105 | David
(2 rows)
```

The rows with id values 102, 103, and 104 are deleted from the target table, and a new row with an id value of 105 and name value of David is inserted into the target table\.

## See also<a name="r_MERGE-see-also"></a>

 [INSERT](r_INSERT_30.md), [UPDATE](r_UPDATE.md), [DELETE](r_DELETE.md) 