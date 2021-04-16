# NEXT\_DAY function<a name="r_NEXT_DAY"></a>

NEXT\_DAY returns the date of the first instance of the specified day that is later than the given date\.

If the *day* value is the same day of the week as *given\_date*, the next occurrence of that day is returned\.

## Syntax<a name="r_NEXT_DAY-synopsis"></a>

```
NEXT_DAY ( { date | timestamp }, day )
```

## Arguments<a name="r_NEXT_DAY-arguments"></a>

 *date* \| *timestamp*  
A date or timestamp column or an expression that implicitly converts to a date or timestamp\.

 *day*   
A string containing the name of any day\. Capitalization does not matter\.  
Valid values are as follows\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_NEXT_DAY.html)

## Return type<a name="r_NEXT_DAY-return-type"></a>

DATE

## Examples<a name="r_NEXT_DAY-example"></a>

The following example returns the date of the first Tuesday after 8/20/2014\.

```
select next_day('2014-08-20','Tuesday');

next_day
-----------
2014-08-26
```

The following example gets target marketing dates for the third quarter\.

```
select username, (firstname ||' '|| lastname) as name,
eventname, caldate, next_day (caldate, 'Monday') as marketing_target
from sales, date, users, event
where sales.buyerid = users.userid
and sales.eventid = event.eventid
and event.dateid = date.dateid
and date.qtr = 3
order by marketing_target, eventname, name;

username  |     name          |   eventname        |    caldate    |   marketing_target
----------+-------------------+--------------------+---------------+------------------
MBO26QSG	Callum Atkinson  .38 Special            2008-07-06	2008-07-07
WCR50YIU	Erasmus Alvarez  A Doll's House         2008-07-03	2008-07-07
CKT70OIE	Hadassah Adkins  Ana Gabriel            2008-07-06	2008-07-07
VVG07OUO	Nathan Abbott    Armando Manzanero      2008-07-04	2008-07-07
GEW77SII	Scarlet Avila    August: Osage County   2008-07-06	2008-07-07
ECR71CVS	Caryn Adkins     Ben Folds              2008-07-03	2008-07-07
KUW82CYU	Kaden Aguilar    Bette Midler           2008-07-01	2008-07-07
WZE78DJZ	Kay Avila        Bette Midler           2008-07-01	2008-07-07
HXY04NVE	Dante Austin     Britney Spears         2008-07-02	2008-07-07
URY81YWF	Wilma Anthony    Britney Spears         2008-07-02	2008-07-07
```