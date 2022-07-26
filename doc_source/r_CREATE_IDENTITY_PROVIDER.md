# CREATE IDENTITY PROVIDER<a name="r_CREATE_IDENTITY_PROVIDER"></a>

Defines a new identity provider\. Only a superuser can create an identity provider\.

## Syntax<a name="r_CREATE_IDENTITY_PROVIDER-synopsis"></a>

```
CREATE IDENTITY PROVIDER identity_provider_name TYPE type_name
NAMESPACE namespace_name 
 PARAMETERS parameter_string
```

## Parameters<a name="r_CREATE_IDENTITY_PROVIDER-parameters"></a>

 *identity\_provider\_name*   
Name of the new identity provider\. For more information about valid names, see [Names and identifiers](r_names.md)\.

*type\_name*  
The identity provider to interface with\. Azure is currently the only supported identity provider\.

*namespace\_name*  
The namespace\. This is a unique, shorthand identifier for the identity provider directory\.

 *parameter\_string*   
A string containing a properly formatted JSON object that contains parameters and values required for the identity provider\.

## Example<a name="r_CREATE_IDENTITY_PROVIDER-examples"></a>

The following example creates an identity provider named *oauth\_standard*, with a TYPE *azure*, to establish communication with Microsoft Azure Active Directory \(AD\)\.

```
CREATE IDENTITY PROVIDER oauth_standard TYPE azure
NAMESPACE 'aad'
PARAMETERS '{"issuer":"https://sts.windows.net/2sdfdsf-d475-420d-b5ac-667adad7c702/",
"client_id":"87f4aa26-78b7-410e-bf29-57b39929ef9a",
"client_secret":"BUAH~ewrqewrqwerUUY^%tHe1oNZShoiU7",
"audience":["https://analysis.windows.net/powerbi/connector/AmazonRedshift"]
}'
```