# SVL\_DATASHARE\_USAGE<a name="r_SVL_DATASHARE_USAGE"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Records the activity and usage of data shares\. This view is only relevant only on the producer cluster\.

SVL\_DATASHARE\_USAGE is visible to all users\.

## Table columns<a name="r_SVL_DATASHARE_USAGE-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_DATASHARE_USAGE.html)

## Sample queries<a name="r_SVL_DATASHARE_USAGE-sample-queries"></a>

The following example shows a SVL\_DATASHARE\_USAGE view\.

```
select * from svl_datashare_usage;
                
 share_id | share_name | request_type |    object_type    | object_oid | object_name | consumer_account |   consumer_namespace                 |     consumer_transaction_uid     |         recordtime         | status |  error
----------+------------+--------------+-------------------+------------+-------------+------------------+--------------------------------------+----------------------------------+----------------------------+--------+---------
   118489 | test_share | GET RELATION | LATE BINDING VIEW |     118495 | test_lbv    | 094458981268     | 50a91f7b-a98e-41af-ae41-45125c83723a | cb01cb6df56067cd79e491989168f440 | 2020-11-23 19:39:07.334509 |      0 |
   118489 | test_share | GET RELATION | TABLE             |     118490 | test_t1     | 094458981268     | 50a91f7b-a98e-41af-ae41-45125c83723a | 97df3003867c80b2d9ebdcc4d22b55f5 | 2020-11-23 19:39:06.92648  |      0 |
   118489 | test_share | GET RELATION | MATERIALIZED VIEW |     118501 | test_mv     | 094458981268     | 50a91f7b-a98e-41af-ae41-45125c83723a | 1748d1b9b0d81983e7863f2a7ffcbe4a | 2020-11-23 19:39:07.524768 |      0 |
   118489 | test_share | GET RELATION | VIEW              |     118492 | test_v      | 094458981268     | 50a91f7b-a98e-41af-ae41-45125c83723a | 4e5ea86ed513841f995c8ca892f1a3a8 | 2020-11-23 19:39:06.845637 |      0 |
(4 rows)
```