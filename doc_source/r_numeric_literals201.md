# Integer and floating\-point literals<a name="r_numeric_literals201"></a>

Literals or constants that represent numbers can be integer or floating\-point\.

## Integer literals<a name="r_numeric_literals201-integer-literals"></a>

An integer constant is a sequence of the digits 0\-9, with an optional positive \(\+\) or negative \(\-\) sign preceding the digits\.

## Syntax<a name="r_numeric_literals201-synopsis"></a>

```
[ + | - ] digit ...
```

## Examples<a name="r_numeric_literals201-examples"></a>

Valid integers include the following:

```
23
-555
+17
```

## Floating\-point literals<a name="r_numeric_literals201-floating-point-literals"></a>

Floating\-point literals \(also referred to as decimal, numeric, or fractional literals\) are sequences of digits that can include a decimal point, and optionally the exponent marker \(e\)\.

## Syntax<a name="r_numeric_literals201-synopsis2"></a>

```
[ + | - ] digit ... [ . ] [ digit ...]
[ e | E [ + | - ] digit ... ]
```

## Arguments<a name="r_numeric_literals201-arguments"></a>

e \| E  
e or E indicates that the number is specified in scientific notation\.

## Examples<a name="r_numeric_literals201-examples2"></a>

Valid floating\-point literals include the following:

```
3.14159
-37.
2.0e19
-2E-19
```