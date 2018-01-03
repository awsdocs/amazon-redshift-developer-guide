# VERSION<a name="r_VERSION"></a>

 The VERSION\(\) function returns details about the currently installed release, with specific Amazon Redshift version information at the end\. 

**Note**  
This is a leader\-node function\. This function returns an error if it references a user\-created table, an STL or STV system table, or an SVV or SVL system view\.

## Syntax<a name="r_VERSION-synopsis"></a>

```
VERSION()
```

## Return Type<a name="r_VERSION-return-type"></a>

Returns a CHAR or VARCHAR string\. 