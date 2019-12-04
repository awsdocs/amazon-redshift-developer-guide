# Querying a Materialized View<a name="mv.query"></a>


|  | 
| --- |
| This is prerelease documentation for the materialized view feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

A materialized view can be used in any SQL query by referencing the materialized view name as the data source, like a table or standard view\. There are no limitations to using materialized views in queries\. 

 When a query accesses a materialized view, it only sees the data that is stored in the materialized view as of its most recent refresh\. Thus, the query might not see all latest changes from corresponding base tables of the materialized view\.

**Note**  
If other users want to query the materialized view, then the owner of the materialized view must `GRANT` the `SELECT` privilege to those other users\. \(It isn't necessary for the other users to have the `SELECT` privilege on the underlying base tables\.\)  
Similarly, the owner of the materialized view can `REVOKE` the `SELECT` privilege from other users, to prevent them from querying the materialized view\.  
If the owner of the materialized view no longer has the `SELECT` privilege on the underlying base tables, then the following is true:  
The owner can no longer query the materialized view\.
Other users who have the `SELECT` privilege on the materialized view can no longer query the materialized view\.

**Example**

The following example queries the `tickets_mv` materialized view \(see [CREATE MATERIALIZED VIEW](mv-create.md)\):

```
SELECT sold
FROM tickets_mv
WHERE catgroup = 'Concerts';
```

Because the query results are precomputed, there's no need to access the underlying tables \(`category`, `event`, `sales`\)\. Amazon Redshift can return the results directly from `tickets_mv`\.