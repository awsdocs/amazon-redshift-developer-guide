# farmFingerprint64 function<a name="r_FARMFINGERPRINT64"></a>

Computes the farmhash value of the input argument using the `Fingerprint64` function\. 

## Syntax<a name="r_FARMFINGERPRINT64-synopsis"></a>

```
farmFingerprint64(expression)
```

## Argument<a name="r_FARMFINGERPRINT64-argument"></a>

 *expression*   
The input expression must be a `VARCHAR` or `VARBYTE` data type\. 

## Return type<a name="r_FARMFINGERPRINT64-return-type"></a>

The `farmFingerprint64` function returns a `BIGINT`\. 

## Example<a name="r_FARMFINGERPRINT64-example"></a>

The following example returns the `farmFingerprint64` value of `Amazon Redshift` that is input as a `VARCHAR` data type\. 

```
SELECT farmFingerprint64('Amazon Redshift');
```

```
  
  farmfingerprint64
---------------------
 8085098817162212970
```

The following example returns the `farmFingerprint64` value of `Amazon Redshift` that is input as a `VARBYTE` data type\. 

```
SELECT farmFingerprint64('Amazon Redshift'::varbyte);
```

```
  
  farmfingerprint64
---------------------
 8085098817162212970
```