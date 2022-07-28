# DROP IDENTITY PROVIDER<a name="r_DROP_IDENTITY_PROVIDER"></a>

Deletes an identity provider\. This command isn't reversible\. Only a superuser can drop an identity provider\.

## Syntax<a name="r_DROP_IDENTITY_PROVIDER-synopsis"></a>

```
DROP IDENTITY PROVIDER identity_provider_name [ CASCADE ]
```

## Parameters<a name="r_DROP_IDENTITY_PROVIDER-parameter"></a>

 *identity\_provider\_name*   
Name of the identity provider to delete\.

 CASCADE   
Deletes users and roles attached to the identity provider, when it is deleted\.

## Example<a name="r_DROP_IDENTITY_PROVIDER-example"></a>

The following example deletes the *oauth\_provider* identity provider\.

```
DROP IDENTITY PROVIDER oauth_provider;
```

If you drop the identity provider, some users may not be able to log in or use client tools configured to use the identity provider\.