# Date and timestamp literals<a name="r_Date_and_time_literals"></a>

## Dates<a name="r_Date_and_time_literals-dates"></a>

The following input dates are all valid examples of literal date values that you can load into Amazon Redshift tables\. The default MDY DateStyle mode is assumed to be in effect, which means that the month value precedes the day value in strings such as `1999-01-08` and `01/02/00`\. 

**Note**  
A date or time stamp literal must be enclosed in quotes when you load it into a table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Date_and_time_literals.html)

## Timestamps<a name="r_Date_and_time_literals-timestamps"></a>

The following input timestamps are all valid examples of literal time values that you can load into Amazon Redshift tables\. All of the valid date literals can be combined with the following time literals\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Date_and_time_literals.html)

## Special datetime values<a name="r_Date_and_time_literals-special-datetime-values"></a>

The following special values can be used as datetime literals and as arguments to date functions\. They require single quotes and are converted to regular timestamp values during query processing\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Date_and_time_literals.html)

The following examples show how `now` and `today` work in conjunction with the DATEADD function: 

```
select dateadd(day,1,'today');

date_add
---------------------
2009-11-17 00:00:00
(1 row)

select dateadd(day,1,'now');

date_add
----------------------------
2009-11-17 10:45:32.021394
(1 row)
```