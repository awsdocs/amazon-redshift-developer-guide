# Pattern\-matching conditions<a name="pattern-matching-conditions"></a>

**Topics**
+ [LIKE](r_patternmatching_condition_like.md)
+ [SIMILAR TO](pattern-matching-conditions-similar-to.md)
+ [POSIX operators](pattern-matching-conditions-posix.md)

A pattern\-matching operator searches a string for a pattern specified in the conditional expression and returns true or false depend on whether it finds a match\. Amazon Redshift uses three methods for pattern matching: 
+ LIKE expressions

  The LIKE operator compares a string expression, such as a column name, with a pattern that uses the wildcard characters `%` \(percent\) and `_` \(underscore\)\. LIKE pattern matching always covers the entire string\. LIKE performs a case\-sensitive match and ILIKE performs a case\-insensitive match\. 
+ SIMILAR TO regular expressions

  The SIMILAR TO operator matches a string expression with a SQL standard regular expression pattern, which can include a set of pattern\-matching metacharacters that includes the two supported by the LIKE operator\. SIMILAR TO matches the entire string and performs a case\-sensitive match\.
+ POSIX\-style regular expressions 

  POSIX regular expressions provide a more powerful means for pattern matching than the LIKE and SIMILAR TO operators\. POSIX regular expression patterns can match any portion of the string and performs a case\-sensitive match\.

Regular expression matching, using SIMILAR TO or POSIX operators, is computationally expensive\. We recommend using LIKE whenever possible, especially when processing a very large number of rows\. For example, the following queries are functionally identical, but the query that uses LIKE executes several times faster than the query that uses a regular expression:

```
select count(*) from event where eventname SIMILAR TO '%(Ring|Die)%'; 
select count(*) from event where eventname LIKE '%Ring%' OR eventname LIKE '%Die%';
```