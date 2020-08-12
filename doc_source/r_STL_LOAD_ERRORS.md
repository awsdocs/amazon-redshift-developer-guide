# STL\_LOAD\_ERRORS<a name="r_STL_LOAD_ERRORS"></a>

Displays the records of all Amazon Redshift load errors\.

STL\_LOAD\_ERRORS contains a history of all Amazon Redshift load errors\. See [Load error reference](r_Load_Error_Reference.md) for a comprehensive list of possible load errors and explanations\.

Query [STL\_LOADERROR\_DETAIL](r_STL_LOADERROR_DETAIL.md) for additional details, such as the exact data row and column where a parse error occurred, after you query STL\_LOAD\_ERRORS to find out general information about the error\. 

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_LOAD_ERRORS-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_LOAD_ERRORS.html)

## Sample queries<a name="r_STL_LOAD_ERRORS-sample-queries2"></a>

The following query joins STL\_LOAD\_ERRORS to STL\_LOADERROR\_DETAIL to view the details errors that occurred during the most recent load\.

```
select d.query, substring(d.filename,14,20), 
d.line_number as line, 
substring(d.value,1,16) as value,
substring(le.err_reason,1,48) as err_reason
from stl_loaderror_detail d, stl_load_errors le
where d.query = le.query
and d.query = pg_last_copy_id(); 

 query |    substring      | line |  value   |              err_reason
-------+-------------------+------+----------+----------------------------
    558| allusers_pipe.txt |  251 | 251      | String contains invalid or 
                                               unsupported UTF8 code
    558| allusers_pipe.txt |  251 | ZRU29FGR | String contains invalid or 
                                               unsupported UTF8 code
    558| allusers_pipe.txt |  251 | Kaitlin  | String contains invalid or 
                                               unsupported UTF8 code
    558| allusers_pipe.txt |  251 | Walter   | String contains invalid or 
                                               unsupported UTF8 code
```

The following example uses STL\_LOAD\_ERRORS with STV\_TBL\_PERM to create a new view, and then uses that view to determine what errors occurred while loading data into the EVENT table: 

```
create view loadview as
(select distinct tbl, trim(name) as table_name, query, starttime,
trim(filename) as input, line_number, colname, err_code,
trim(err_reason) as reason
from stl_load_errors sl, stv_tbl_perm sp
where sl.tbl = sp.id);
```

Next, the following query actually returns the last error that occurred while loading the EVENT table: 

```
select table_name, query, line_number, colname, starttime, 
trim(reason) as error
from loadview
where table_name ='event'
order by line_number limit 1;
```

The query returns the last load error that occurred for the EVENT table\. If no load errors occurred, the query returns zero rows\. In this example, the query returns a single error: 

```
 table_name | query | line_number | colname | error | starttime
------+-----+----+----+--------------------------------------------------------+----------------------
event | 309 |  0 |  5 | Error in Timestamp value or format [%Y-%m-%d %H:%M:%S] | 2014-04-22 15:12:44

(1 row)
```