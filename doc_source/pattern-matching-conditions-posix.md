# POSIX operators<a name="pattern-matching-conditions-posix"></a>

POSIX regular expressions provide a more powerful means for pattern matching than the [LIKE](r_patternmatching_condition_like.md) and [SIMILAR TO](pattern-matching-conditions-similar-to.md) operators\. POSIX regular expression patterns can match any portion of a string, unlike the SIMILAR TO operator, which returns true only if its pattern matches the entire string\.

**Note**  
Regular expression matching using POSIX operators is computationally expensive\. We recommend using LIKE whenever possible, especially when processing a very large number of rows\. For example, the following queries are functionally identical, but the query that uses LIKE executes several times faster than the query that uses a regular expression:  

```
select count(*) from event where eventname ~ '.*(Ring|Die).*'; 
select count(*) from event where eventname LIKE '%Ring%' OR eventname LIKE '%Die%';
```

## Syntax<a name="pattern-matching-conditions-posix-synopsis"></a>

```
expression [ ! ] ~ pattern
```

## Arguments<a name="pattern-matching-conditions-posix-arguments"></a>

 *expression*   
A valid UTF\-8 character expression, such as a column name\. 

\!  
Negation operator\.

\~  
Perform a case\-sensitive match for any substring of *expression*\. 

 *pattern*   
A string literal that represents a SQL standard regular expression pattern\. 

If *pattern* does not contain wildcard characters, then the pattern only represents the string itself\.

To search for strings that include metacharacters, such as ‘`. * | ? `‘, and so on, escape the character using two backslashes \('` \\` '\)\. Unlike `SIMILAR TO` and `LIKE`, POSIX regular expression syntax does not support a user\-defined escape character\. 

Either of the character expressions can be CHAR or VARCHAR data types\. If they differ, Amazon Redshift converts *pattern* to the data type of *expression*\. 

All of the character expressions can be CHAR or VARCHAR data types\. If the expressions differ in data type, Amazon Redshift converts them to the data type of *expression*\. 

POSIX pattern matching supports the following metacharacters: 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/pattern-matching-conditions-posix.html)

Amazon Redshift supports the following POSIX character classes\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/pattern-matching-conditions-posix.html)

 Amazon Redshift supports the following Perl\-influenced operators in regular expressions\. Escape the operator using two backslashes \(‘`\\`’\)\.   

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/pattern-matching-conditions-posix.html)

## Examples<a name="pattern-matching-conditions-posix-synopsis-examples"></a>

The following table shows examples of pattern matching using POSIX operators:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/pattern-matching-conditions-posix.html)

The following example finds all cities whose names contain `E` or `H`: 

```
select distinct city from users
where city ~ '.*E.*|.*H.*' order by city;
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

The following example uses the escape string \('`\\`'\) to search for strings that include a period\. 

```
select venuename from venue
where venuename ~ '.*\\..*';

       venuename
-----------------------------
Bernard B. Jacobs Theatre
E.J. Nutter Center
Hubert H. Humphrey Metrodome
Jobing.com Arena
St. James Theatre
St. Pete Times Forum
Superpages.com Center
U.S. Cellular Field
```