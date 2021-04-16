# LTRIM function<a name="r_LTRIM"></a>

The LTRIM function trims a specified set of characters from the beginning of a string\. 

## Syntax<a name="r_LTRIM-synopsis"></a>

```
LTRIM( string, 'trim_chars' )
```

## Arguments<a name="r_LTRIM-arguments"></a>

 *string*   
The string column or expression to be trimmed\.

 *trim\_chars*   
A string column or expression representing the characters to be trimmed from the beginning of *string*\.

## Return type<a name="r_LTRIM-return-type"></a>

The LTRIM function returns a character string that is the same data type as the input string \(CHAR or VARCHAR\)\. 

## Example<a name="r_LTRIM-example"></a>

The following example trims the year from LISTTIME: 

```
select listid, listtime, ltrim(listtime, '2008-')
from listing
order by 1, 2, 3
limit 10;

listid |      listtime       |     ltrim
-------+---------------------+----------------
     1 | 2008-01-24 06:43:29 | 1-24 06:43:29
     2 | 2008-03-05 12:25:29 | 3-05 12:25:29
     3 | 2008-11-01 07:35:33 | 11-01 07:35:33
     4 | 2008-05-24 01:18:37 | 5-24 01:18:37
     5 | 2008-05-17 02:29:11 | 5-17 02:29:11
     6 | 2008-08-15 02:08:13 | 15 02:08:13
     7 | 2008-11-15 09:38:15 | 11-15 09:38:15
     8 | 2008-11-09 05:07:30 | 11-09 05:07:30
     9 | 2008-09-09 08:03:36 | 9-09 08:03:36
    10 | 2008-06-17 09:44:54 | 6-17 09:44:54
(10 rows)
```

LTRIM removes any of the characters in *trim\_chars* when they appear at the beginning of *string*\. The following example trims the characters 'C', 'D', and 'G' when they appear at the beginning of VENUENAME\. 

```
select venueid, venuename, trim(venuename, 'CDG')
from venue
where venuename like '%Park'
order by 2
limit 7;

venueid | venuename                  | btrim                    
--------+----------------------------+--------------------------
    121 | ATT Park                   | ATT Park                
    109 | Citizens Bank Park         | itizens Bank Park        
    102 | Comerica Park              | omerica Park             
      9 | Dick's Sporting Goods Park | ick's Sporting Goods Park
     97 | Fenway Park                | Fenway Park              
    112 | Great American Ball Park   | reat American Ball Park  
    114 | Miller Park                | Miller Park
```