# SVV\_IDENTITY\_PROVIDERS<a name="r_SVV_IDENTITY_PROVIDERS"></a>

The SVV\_IDENTITY\_PROVIDERS view returns the name and additional properties for identity providers\. For more information about how to create an identity provider, see [CREATE IDENTITY PROVIDER](r_CREATE_IDENTITY_PROVIDER.md)\.

SVV\_IDENTITY\_PROVIDERS is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="SVV_IDENTITY_PROVIDERS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_IDENTITY_PROVIDERS.html)

### Sample queries<a name="SVV_IDENTITY_PROVIDERS-sample-queries"></a>

Run a query like the following, after creating identity providers, to view their properties\.

```
SELECT name, type, instanceid, namespc, params, enabled 
FROM svv_identity_providers 
ORDER BY 1;
```

The sample output includes param descriptions\.

```
       name       | type  |              instanceid              | namespc |                                                                                                                                                params                                                                                                                                                 | enabled 
------------------+-------+--------------------------------------+---------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------
 rs5517_azure_idp | azure | e40d4bb2-7670-44ae-bfb8-5db013221d73 | abc     | {"issuer":"https://login.microsoftonline.com/e40d4bb2-7670-44ae-bfb8-5db013221d73/v2.0", "client_id":"871c010f-5e61-4fb1-83ac-98610a7e9110", "client_secret":, "audience":["https://analysis.windows.net/powerbi/connector/AmazonRedshift", "https://analysis.windows.net/powerbi/connector/AWSRDS"]} | t
(1 row)
```