# CURRENT\_NAMESPACE<a name="r_CURRENT_NAMESPACE"></a>

Returns the namespace of the current Amazon Redshift cluster\.

## Syntax<a name="r_CURRENT_NAMESPACE-synopsis"></a>

```
current_namespace
```

## Return type<a name="r_CURRENT_NAMESPACE-return-type"></a>

Returns a CHAR or VARCHAR string\. 

## Example<a name="r_CURRENT_NAMESPACE-example"></a>

The following query returns the name of the current namespace\.  

```
select user, current_namespace; 
current_user | current_namespace
-------------+-------------------------------------
dwuser       | 86b5169f-01dc-4a6f-9fbb-e2e24359e9a8

(1 row)
```