# Datetime format strings<a name="r_FORMAT_strings"></a>

You can find a reference for datetime format strings following\. 

The following format strings apply to functions such as TO\_CHAR\. These strings can contain datetime separators \(such as '`-`', '`/`', or '`:`'\) and the following "dateparts" and "timeparts"\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_FORMAT_strings.html)

**Note**  
You must surround datetime separators \(such as '\-', '/' or ':'\) with single quotation marks, but you must surround the "dateparts" and "timeparts" listed in the preceding table with double quotation marks\.

The following example shows formatting for seconds, milliseconds, and microseconds\.

```
select sysdate,
to_char(sysdate, 'HH24:MI:SS') as seconds,
to_char(sysdate, 'HH24:MI:SS.MS') as milliseconds,
to_char(sysdate, 'HH24:MI:SS:US') as microseconds;

timestamp           | seconds  | milliseconds | microseconds   
--------------------+----------+--------------+----------------
2015-04-10 18:45:09 | 18:45:09 | 18:45:09.325 | 18:45:09:325143
```