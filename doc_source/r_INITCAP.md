# INITCAP function<a name="r_INITCAP"></a>

Capitalizes the first letter of each word in a specified string\. INITCAP supports UTF\-8 multibyte characters, up to a maximum of four bytes per character\.

## Syntax<a name="r_INITCAP-synopsis"></a>

```
INITCAP(string)
```

## Argument<a name="r_INITCAP-argument"></a>

 *string*   
The input parameter is a CHAR or VARCHAR string\. 

## Return type<a name="r_INITCAP-return-type"></a>

The INITCAP function returns a VARCHAR string\.

## Usage notes<a name="r_INITCAP_usage_notes"></a>

The INITCAP function makes the first letter of each word in a string uppercase, and any subsequent letters are made \(or left\) lowercase\. Therefore, it is important to understand which characters \(other than space characters\) function as word separators\. A *word separator* character is any non\-alphanumeric character, including punctuation marks, symbols, and control characters\. All of the following characters are word separators: 

```
! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { | } ~ 
```

Tabs, newline characters, form feeds, line feeds, and carriage returns are also word separators\.

## Examples<a name="r_INITCAP-examples"></a>

The following example capitalizes the initials of each word in the CATDESC column: 

```
select catid, catdesc, initcap(catdesc)
from category
order by 1, 2, 3;

 catid |                  catdesc                   |                  initcap
-------+--------------------------------------------+--------------------------------------------
     1 | Major League Baseball                      | Major League Baseball
     2 | National Hockey League                     | National Hockey League
     3 | National Football League                   | National Football League
     4 | National Basketball Association            | National Basketball Association
     5 | Major League Soccer                        | Major League Soccer
     6 | Musical theatre                            | Musical Theatre
     7 | All non-musical theatre                    | All Non-Musical Theatre
     8 | All opera and light opera                  | All Opera And Light Opera
     9 | All rock and pop music concerts            | All Rock And Pop Music Concerts
    10 | All jazz singers and bands                 | All Jazz Singers And Bands
    11 | All symphony, concerto, and choir concerts | All Symphony, Concerto, And Choir Concerts
(11 rows)
```

The following example shows that the INITCAP function does not preserve uppercase characters when they do not begin words\. For example, MLB becomes Mlb\.

```
select initcap(catname)
from category
order by catname;

initcap
-----------
Classical
Jazz
Mlb
Mls
Musicals
Nba
Nfl
Nhl
Opera
Plays
Pop
(11 rows)
```

The following example shows that non\-alphanumeric characters other than spaces function as word separators, causing uppercase characters to be applied to several letters in each string:

```
select email, initcap(email)
from users
order by userid desc limit 5;

    email	                        |	initcap
------------------------------------+------------------------------------
 urna.Ut@egetdictumplacerat.edu	   | Urna.Ut@Egetdictumplacerat.Edu
 nibh.enim@egestas.ca	            | Nibh.Enim@Egestas.Ca
 in@Donecat.ca	                     | In@Donecat.Ca
 sodales@blanditviverraDonec.ca     | Sodales@Blanditviverradonec.Ca
 sociis.natoque.penatibus@vitae.org | Sociis.Natoque.Penatibus@Vitae.Org
(5 rows)
```