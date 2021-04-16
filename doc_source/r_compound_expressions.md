# Compound expressions<a name="r_compound_expressions"></a>

A compound expression is a series of simple expressions joined by arithmetic operators\. A simple expression used in a compound expression must return a numeric value\.

## Syntax<a name="r_compound_expressions-synopsis"></a>

```
expression 
operator 
expression | (compound_expression)
```

## Arguments<a name="r_compound_expressions-arguments"></a>

 *expression*   
A simple expression that evaluates to a value\.

 *operator*   
 A compound arithmetic expression can be constructed using the following operators, in this order of precedence:  
+ \( \) : parentheses to control the order of evaluation
+ \+ , \- : positive and negative sign/operator
+  ^ , \|/ , \|\|/ : exponentiation, square root, cube root
+ \* , / , % : multiplication, division, and modulo operators
+  @ : absolute value
+ \+ , \- : addition and subtraction
+ & , \|, \#, \~, <<, >> : AND, OR, NOT, shift left, shift right bitwise operators
+ \|\|: concatenation

 *\(compound\_expression\)*   
Compound expressions can be nested using parentheses\.

## Examples<a name="r_compound_expressions-examples"></a>

Examples of compound expressions include the following\.

```
('SMITH' || 'JONES')
sum(x) / y
sqrt(256) * avg(column)
rank() over (order by qtysold) / 100
(select (pricepaid - commission) from sales where dateid = 1882) * (qtysold)
```

Some functions can also be nested within other functions\. For example, any scalar function can nest within another scalar function\. The following example returns the sum of the absolute values of a set of numbers:

```
sum(abs(qtysold))
```

Window functions cannot be used as arguments for aggregate functions or other window functions\. The following expression would return an error:

```
avg(rank() over (order by qtysold))
```

Window functions can have a nested aggregate function\. The following expression sums sets of values and then ranks them:

```
rank() over (order by sum(qtysold))
```