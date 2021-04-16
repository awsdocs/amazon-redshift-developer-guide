# PG\_GET\_LATE\_BINDING\_VIEW\_COLS<a name="PG_GET_LATE_BINDING_VIEW_COLS"></a>

Returns the column metadata for all late\-binding views in the database\. For more information, see [Late\-binding views](r_CREATE_VIEW.md#r_CREATE_VIEW_late-binding-views)

## Syntax<a name="PG_GET_LATE_BINDING_VIEW_COLS-synopsis"></a>

```
pg_get_late_binding_view_cols()
```

## Return type<a name="PG_GET_LATE_BINDING_VIEW_COLS-return-type"></a>

VARCHAR 

## Usage notes<a name="PG_GET_LATE_BINDING_VIEW_COLS-usage-notes"></a>

The `PG_GET_LATE_BINDING_VIEW_COLS` function returns one row for each column in late\-binding views\. The row contains a comma\-separated list with the schema name, relation name, column name, data type, and column number\.

## Example<a name="PG_GET_LATE_BINDING_VIEW_COLS-example"></a>

The following example returns the column metadata for all late\-binding views\.

```
select pg_get_late_binding_view_cols();

pg_get_late_binding_view_cols                               
------------------------------------------------------------
(public,myevent,eventname,"character varying(200)",1)       
(public,sales_lbv,salesid,integer,1)                        
(public,sales_lbv,listid,integer,2)                         
(public,sales_lbv,sellerid,integer,3)                       
(public,sales_lbv,buyerid,integer,4)                        
(public,sales_lbv,eventid,integer,5)                        
(public,sales_lbv,dateid,smallint,6)                        
(public,sales_lbv,qtysold,smallint,7)                       
(public,sales_lbv,pricepaid,"numeric(8,2)",8)               
(public,sales_lbv,commission,"numeric(8,2)",9)              
(public,sales_lbv,saletime,"timestamp without time zone",10)
(public,event_lbv,eventid,integer,1)                        
(public,event_lbv,venueid,smallint,2)                       
(public,event_lbv,catid,smallint,3)                         
(public,event_lbv,dateid,smallint,4)                        
(public,event_lbv,eventname,"character varying(200)",5)     
(public,event_lbv,starttime,"timestamp without time zone",6)
```

The following example returns the column metadata for all late\-binding views in table format\.

```
select * from pg_get_late_binding_view_cols() cols(view_schema name, view_name name, col_name name, col_type varchar, col_num int);
view_schema | view_name | col_name   | col_type                    | col_num
------------+-----------+------------+-----------------------------+--------
public      | sales_lbv | salesid    | integer                     |       1
public      | sales_lbv | listid     | integer                     |       2
public      | sales_lbv | sellerid   | integer                     |       3
public      | sales_lbv | buyerid    | integer                     |       4
public      | sales_lbv | eventid    | integer                     |       5
public      | sales_lbv | dateid     | smallint                    |       6
public      | sales_lbv | qtysold    | smallint                    |       7
public      | sales_lbv | pricepaid  | numeric(8,2)                |       8
public      | sales_lbv | commission | numeric(8,2)                |       9
public      | sales_lbv | saletime   | timestamp without time zone |      10
public      | event_lbv | eventid    | integer                     |       1
public      | event_lbv | venueid    | smallint                    |       2
public      | event_lbv | catid      | smallint                    |       3
public      | event_lbv | dateid     | smallint                    |       4
public      | event_lbv | eventname  | character varying(200)      |       5
public      | event_lbv | starttime  | timestamp without time zone |       6
```