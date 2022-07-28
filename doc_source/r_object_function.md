# object function<a name="r_object_function"></a>

Creates an object of the SUPER data type\.

## Syntax<a name="r_object-synopsis"></a>

```
OBJECT ( [ key1, value1 ] [, key2, value2 ...] )
```

## Arguments<a name="r_object-arguments"></a>

*key1, key2*  
Expressions that evaluate to VARCHAR type strings\.

*value1, value2*  
Expressions of any Amazon Redshift data type except datetime types, since Amazon Redshift doesn't cast datetime types to the SUPER data type\. For more information on datetime types, see [Datetime types](r_Datetime_types.md)\.  
`value` expressions in an object don't need to be of the same data type\.

## Returns<a name="r_object-returns"></a>

The object function returns the SUPER data type\.

## Example<a name="r_object_example"></a>

```
               --creates an empty object
               select object();
               object
               --------
               {}
               (1 row)
            
               --creates objects with different keys and values
               select object('a', 1, 'b', true, 'c', 3.14);
                         object
               ---------------------------
                {"a":1,"b":true,"c":3.14}
               (1 row)
               
               select object('a', object('aa', 1), 'b', array(2,3), 'c', json_parse('{}'));
               object
               ---------------------------------
                {"a":{"aa":1},"b":[2,3],"c":{}}
               (1 row)
            
               --creates objects using columns from a table
               create table bar (k varchar, v super);
               insert into bar values ('k1', json_parse('[1]')), ('k2', json_parse('{}'));
               select object(k, v) from bar;
               ------------
               {"k1":[1]}
               {"k2":{}}
               (2 rows)
            
               --errors out because DATE type can't be converted to SUPER type
               select object('k', '2008-12-31'::date);
               ERROR:  OBJECT could not convert type date to super
```