# SIMILAR TO<a name="pattern-matching-conditions-similar-to"></a>

The SIMILAR TO operator matches a string expression, such as a column name, with a SQL standard regular expression pattern\. A SQL regular expression pattern can include a set of pattern\-matching metacharacters, including the two supported by the [LIKE](r_patternmatching_condition_like.md) operator\. 

The SIMILAR TO operator returns true only if its pattern matches the entire string, unlike POSIX regular expression behavior, where the pattern can match any portion of the string\. 

SIMILAR TO performs a case\-sensitive match\. 

**Note**  
Regular expression matching using SIMILAR TO is computationally expensive\. We recommend using LIKE whenever possible, especially when processing a very large number of rows\. For example, the following queries are functionally identical, but the query that uses LIKE executes several times faster than the query that uses a regular expression:  

```
select count(*) from event where eventname SIMILAR TO '%(Ring|Die)%'; 
select count(*) from event where eventname LIKE '%Ring%' OR eventname LIKE '%Die%';
```

## Syntax<a name="pattern-matching-conditions-similar-to-synopsis"></a>

```
expression [ NOT ] SIMILAR TO pattern [ ESCAPE 'escape_char' ]
```

## Arguments<a name="pattern-matching-conditions-similar-to-arguments"></a>

 *expression*   
A valid UTF\-8 character expression, such as a column name\. 

SIMILAR TO  
SIMILAR TO performs a case\-sensitive pattern match for the entire string in *expression*\. 

 *pattern*   
A valid UTF\-8 character expression representing a SQL standard regular expression pattern\. 

 *escape\_char*   
A character expression that will escape metacharacters in the pattern\. The default is two backslashes \('\\\\'\)\. 

If *pattern* does not contain metacharacters, then the pattern only represents the string itself\.

Either of the character expressions can be CHAR or VARCHAR data types\. If they differ, Amazon Redshift converts *pattern* to the data type of *expression*\. 

SIMILAR TO supports the following pattern\-matching metacharacters: 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/pattern-matching-conditions-similar-to.html)

## Examples<a name="pattern-matching-conditions-similar-to-examples"></a>

The following table shows examples of pattern matching using SIMILAR TO:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/pattern-matching-conditions-similar-to.html)

The following example finds all cities whose names contain "E" or "H": 

```
select distinct city from users
where city similar to '%E%|%H%' order by city;
         city
-----------------------
 Agoura Hills
 Auburn Hills
 Benton Harbor
 Beverly Hills
 Chicago Heights
 Chino Hills
 Citrus Heights
 East Hartford
```

The following example uses the default escape string \('`\\`'\) to search for strings that include "`_`":

```
select tablename, "column" from pg_table_def 
where "column" similar to '%start\\_%'
limit 5;
     tablename     |    column
-------------------+---------------
 stl_s3client      | start_time
 stl_tr_conflict   | xact_start_ts
 stl_undone        | undo_start_ts
 stl_unload_log    | start_time
 stl_vacuum_detail | start_row
(5 rows)
```

The following example specifies '`^`' as the escape string, then uses the escape string to search for strings that include "`_`": 

```
select tablename, "column" from pg_table_def 
where "column" similar to '%start^_%' escape '^' 
limit 5;
     tablename     |    column
-------------------+---------------
 stl_s3client      | start_time
 stl_tr_conflict   | xact_start_ts
 stl_undone        | undo_start_ts
 stl_unload_log    | start_time
 stl_vacuum_detail | start_row
(5 rows)
```