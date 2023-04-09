# ALTER IDENTITY PROVIDER<a name="r_ALTER_IDENTITY_PROVIDER"></a>

Alters an identity provider to assign new parameters and values\. When you run this command, all previously set parameter values are deleted before the new values are assigned\. Only a superuser can alter an identity provider\.

## Syntax<a name="r_ALTER_IDENTITY_PROVIDER-synopsis"></a>

```
ALTER IDENTITY PROVIDER identity_provider_name
 PARAMETERS parameter_string
```

## Parameters<a name="r_ALTER_IDENTITY_PROVIDER-parameters"></a>

 *identity\_provider\_name*   
Name of the new identity provider\. For more information about valid names, see [Names and identifiers](r_names.md)\.

 *parameter\_string*   
A string containing a properly formatted JSON object that contains parameters and values required for the specific identity provider\.

## Example<a name="r_ALTER_IDENTITY_PROVIDER-examples"></a>

The following example alters an identity provider named *oauth\_standard*\.

```
ALTER IDENTITY PROVIDER oauth_standard
PARAMETERS '{"issuer":"https://sts.windows.net/2sdfdsf-d475-420d-b5ac-667adad7c702/",
"client_id":"87f4aa26-78b7-410e-bf29-57b39929ef9a",
"client_secret":"BUAH~ewrqewrqwerUUY^%tHe1oNZShoiU7",
"audience":["https://analysis.windows.net/powerbi/connector/AmazonRedshift"]
}'
```