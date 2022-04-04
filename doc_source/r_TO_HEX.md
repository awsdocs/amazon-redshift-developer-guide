# TO\_HEX function<a name="r_TO_HEX"></a>

TO\_HEX converts a number or binary value to a hexadecimal representation\. 

## Syntax<a name="r_TO_HEX-synopsis"></a>

```
to_hex(value)
```

## Arguments<a name="r_TO_HEX-arguments"></a>

 *value*   
Either a number or binary value \(VARBYTE\) to be converted\. 

## Return type<a name="r_TO_HEX-return-type"></a>

VARCHAR

## Examples<a name="r_TO_HEX-examples"></a>

The following example converts a number to its hexadecimal representation\. 

```
select to_hex(2147676847);
               
 to_hex
----------
8002f2af
```

The following example converts the VARBYTE representation of `'abc'` to a hexadecimal number\. 

```
select to_hex('abc'::varbyte);
               
 to_hex
----------
 616263
```

The following example creates a table, inserts the VARBYTE representation of `'abc'` to a hexadecimal number, then selects the column with the value\.

```
create table t (vc varchar);
insert into t select to_hex('abc'::varbyte);
select vc from t;
 
   vc
----------
 616263
```

The following example shows that when casting a VARBYTE value to VARCHAR the format is UTF\-8\.

```
create table t (vc varchar);
insert into t select 'abc'::varbyte::varchar;

select vc from t;

 vc
----------
 abc
```