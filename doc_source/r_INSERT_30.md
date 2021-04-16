# INSERT<a name="r_INSERT_30"></a>

**Topics**
+ [Syntax](#r_INSERT_30-synopsis)
+ [Parameters](#r_INSERT_30-parameters)
+ [Usage notes](#r_INSERT_30_usage_notes)
+ [INSERT examples](c_Examples_of_INSERT_30.md)

Inserts new rows into a table\. You can insert a single row with the VALUES syntax, multiple rows with the VALUES syntax, or one or more rows defined by the results of a query \(INSERT INTO\.\.\.SELECT\)\.

**Note**  
We strongly encourage you to use the [COPY](r_COPY.md) command to load large amounts of data\. Using individual INSERT statements to populate a table might be prohibitively slow\. Alternatively, if your data already exists in other Amazon Redshift database tables, use INSERT INTO SELECT or [CREATE TABLE AS](r_CREATE_TABLE_AS.md) to improve performance\. For more information about using the COPY command to load tables, see [Loading data](t_Loading_data.md)\.

**Note**  
The maximum size for a single SQL statement is 16 MB\.

## Syntax<a name="r_INSERT_30-synopsis"></a>

```
INSERT INTO table_name [ ( column [, ...] ) ]
{DEFAULT VALUES |
VALUES ( { expression | DEFAULT } [, ...] )
[, ( { expression | DEFAULT } [, ...] )
[, ...] ] |
query }
```

## Parameters<a name="r_INSERT_30-parameters"></a>

 *table\_name*   
A temporary or persistent table\. Only the owner of the table or a user with INSERT privilege on the table can insert rows\. If you use the *query* clause to insert rows, you must have SELECT privilege on the tables named in the query\.   
Use INSERT \(external table\) to insert results of a SELECT query into existing tables on external catalog\. For more information, see [INSERT \(external table\)](r_INSERT_external_table.md)\.

 *column*   
You can insert values into one or more columns of the table\. You can list the target column names in any order\. If you don't specify a column list, the values to be inserted must correspond to the table columns in the order in which they were declared in the CREATE TABLE statement\. If the number of values to be inserted is less than the number of columns in the table, the first *n* columns are loaded\.   
Either the declared default value or a null value is loaded into any column that isn't listed \(implicitly or explicitly\) in the INSERT statement\. 

DEFAULT VALUES   
If the columns in the table were assigned default values when the table was created, use these keywords to insert a row that consists entirely of default values\. If any of the columns don't have default values, nulls are inserted into those columns\. If any of the columns are declared NOT NULL, the INSERT statement returns an error\. 

VALUES   
Use this keyword to insert one or more rows, each row consisting of one or more values\. The VALUES list for each row must align with the column list\. To insert multiple rows, use a comma delimiter between each list of expressions\. Do not repeat the VALUES keyword\. All VALUES lists for a multiple\-row INSERT statement must contain the same number of values\. 

 *expression*   
A single value or an expression that evaluates to a single value\. Each value must be compatible with the data type of the column where it is being inserted\. If possible, a value whose data type doesn't match the column's declared data type is automatically converted to a compatible data type\. For example:   
+ A decimal value `1.1` is inserted into an INT column as `1`\. 
+ A decimal value `100.8976` is inserted into a DEC\(5,2\) column as `100.90`\. 
You can explicitly convert a value to a compatible data type by including type cast syntax in the expression\. For example, if column COL1 in table T1 is a CHAR\(3\) column:   

```
insert into t1(col1) values('Incomplete'::char(3));
```
This statement inserts the value `Inc` into the column\.   
For a single\-row INSERT VALUES statement, you can use a scalar subquery as an expression\. The result of the subquery is inserted into the appropriate column\.   
Subqueries aren't supported as expressions for multiple\-row INSERT VALUES statements\. 

DEFAULT   
Use this keyword to insert the default value for a column, as defined when the table was created\. If no default value exists for a column, a null is inserted\. You can't insert a default value into a column that has a NOT NULL constraint if that column doesn't have an explicit default value assigned to it in the CREATE TABLE statement\. 

 *query*   
Insert one or more rows into the table by defining any query\. All of the rows that the query produces are inserted into the table\. The query must return a column list that is compatible with the columns in the table, but the column names don't have to match\. 

## Usage notes<a name="r_INSERT_30_usage_notes"></a>

**Note**  
We strongly encourage you to use the [COPY](r_COPY.md) command to load large amounts of data\. Using individual INSERT statements to populate a table might be prohibitively slow\. Alternatively, if your data already exists in other Amazon Redshift database tables, use INSERT INTO SELECT or [CREATE TABLE AS](r_CREATE_TABLE_AS.md) to improve performance\. For more information about using the COPY command to load tables, see [Loading data](t_Loading_data.md)\.

The data format for the inserted values must match the data format specified by the CREATE TABLE definition\. 

 After inserting a large number of new rows into a table: 
+ Vacuum the table to reclaim storage space and re\-sort rows\. 
+ Analyze the table to update statistics for the query planner\. 

When values are inserted into DECIMAL columns and they exceed the specified scale, the loaded values are rounded up as appropriate\. For example, when a value of `20.259` is inserted into a DECIMAL\(8,2\) column, the value that is stored is `20.26`\. 

You can insert into a GENERATED BY DEFAULT AS IDENTITY column\. You can update columns defined as GENERATED BY DEFAULT AS IDENTITY with values that you supply\. For more information, see [GENERATED BY DEFAULT AS IDENTITY](r_CREATE_TABLE_NEW.md#identity-generated-bydefault-clause)\. 