# PG\_GET\_COLS<a name="PG_GET_COLS"></a>

Returns the column metadata for a table or view definition\.

## Syntax<a name="PG_GET_COLS-synopsis"></a>

```
pg_get_cols('name')
```

## Arguments<a name="PG_GET_COLS-arguments"></a>

 *name*   
The name of an Amazon Redshift table or view\.

## Return type<a name="PG_GET_COLS-return-type"></a>

VARCHAR 

## Usage notes<a name="PG_GET_COLS-usage-notes"></a>

The PG\_GET\_COLS function returns one row for each column in the table or view definition\. The row contains a comma\-separated list with the schema name, relation name, column name, data type, and column number\. 

## Example<a name="PG_GET_COLS-example"></a>

The following example returns the column metadata for a view named SALES\_VW\.

```
select pg_get_cols('sales_vw');

pg_get_cols                                                
-----------------------------------------------------------
(public,sales_vw,salesid,integer,1)                        
(public,sales_vw,listid,integer,2)                         
(public,sales_vw,sellerid,integer,3)                       
(public,sales_vw,buyerid,integer,4)                        
(public,sales_vw,eventid,integer,5)                        
(public,sales_vw,dateid,smallint,6)                        
(public,sales_vw,qtysold,smallint,7)                       
(public,sales_vw,pricepaid,"numeric(8,2)",8)               
(public,sales_vw,commission,"numeric(8,2)",9)              
(public,sales_vw,saletime,"timestamp without time zone",10)
```

The following example returns the column metadata for the SALES\_VW view in table format\.

```
select * from pg_get_cols('sales_vw') 
cols(view_schema name, view_name name, col_name name, col_type varchar, col_num int);

view_schema | view_name | col_name   | col_type                    | col_num
------------+-----------+------------+-----------------------------+--------
public      | sales_vw  | salesid    | integer                     |       1
public      | sales_vw  | listid     | integer                     |       2
public      | sales_vw  | sellerid   | integer                     |       3
public      | sales_vw  | buyerid    | integer                     |       4
public      | sales_vw  | eventid    | integer                     |       5
public      | sales_vw  | dateid     | smallint                    |       6
public      | sales_vw  | qtysold    | smallint                    |       7
public      | sales_vw  | pricepaid  | numeric(8,2)                |       8
public      | sales_vw  | commission | numeric(8,2)                |       9
public      | sales_vw  | saletime   | timestamp without time zone |      10
```