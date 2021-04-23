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

The following example finds cities whose names contain "E" or "H": 

```
SELECT DISTINCT city FROM users
WHERE city SIMILAR TO '%E%|%H%' ORDER BY city LIMIT 5;

      city
-----------------
 Agoura Hills
 Auburn Hills
 Benton Harbor
 Beverly Hills
 Chicago Heights
```

The following example uses the default escape string \('`\\`'\) to search for strings that include "`_`":

```
SELECT tablename, "column" FROM pg_table_def
WHERE "column" SIMILAR TO '%start\\_%'
ORDER BY tablename, "column" LIMIT 5;

        tablename         |       column
--------------------------+---------------------
 stcs_abort_idle          | idle_start_time
 stcs_abort_idle          | txn_start_time
 stcs_analyze_compression | start_time
 stcs_auto_worker_levels  | start_level
 stcs_auto_worker_levels  | start_wlm_occupancy
```

The following example specifies '`^`' as the escape string, then uses the escape string to search for strings that include "`_`": 

```
SELECT tablename, "column" FROM pg_table_def
WHERE "column" SIMILAR TO '%start^_%' ESCAPE '^'
ORDER BY tablename, "column" LIMIT 5;

        tablename         |       column
--------------------------+---------------------
 stcs_abort_idle          | idle_start_time
 stcs_abort_idle          | txn_start_time
 stcs_analyze_compression | start_time
 stcs_auto_worker_levels  | start_level
 stcs_auto_worker_levels  | start_wlm_occupancy
```