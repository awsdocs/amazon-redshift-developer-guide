# Dynamic data masking<a name="t_ddm"></a>

## Overview<a name="ddm-overview"></a>

Using dynamic data masking \(DDM\) in Amazon Redshift, you can protect sensitive data in your data warehouse\. You can manipulate how Amazon Redshift shows sensitive data to the user at query time, without transforming it in the database\. You control access to data through masking policies that apply custom obfuscation rules to a given user or role\. In that way, you can respond to changing privacy requirements without altering underlying data or editing SQL queries\.

Dynamic data masking policies hide, obfuscate, or pseudonymize data that matches a given format\. When attached to a table, the masking expression is applied to one or more of its columns\. You can further modify masking policies to only apply them to certain users, or to user\-defined roles that you can create with [Role\-based access control \(RBAC\)](t_Roles.md)\. Additionally, you can apply DDM on the cell level by using conditional columns when creating your masking policy\. For more information on conditional masking, see [Conditional dynamic data masking](t_ddm-conditional.md)\.

You can apply multiple masking policies with varying levels of obfuscation to the same column in a table and assign them to different roles\. To avoid conflicts when you have different roles with different policies applying to one column, you can set priorities for each application\. In that way, you can control what data a given user or role can access\. DDM policies can partially or completely redact data, or hash it by using user\-defined functions written in SQL, Python, or with AWS Lambda\. By masking data using hashes, you can apply joins on this data without access to potentially sensitive information\.

## End\-to\-end example<a name="ddm-example"></a>

The following is an end\-to\-end example showing how you can create and attach masking policies to a column\. These policies let users access a column and see different values, depending on the degree of obfuscation in the policies attached to their roles\. You must be a superuser or have the [https://docs.aws.amazon.com/redshift/latest/dg/r_roles-default.html](https://docs.aws.amazon.com/redshift/latest/dg/r_roles-default.html) role to run this example\.

### Creating a masking policy<a name="ddm-example-create"></a>

First, create a table and populate it with credit card values\.

```
--create the table         
CREATE TABLE credit_cards (
  customer_id INT,
  credit_card TEXT
);

--populate the table with sample values
INSERT INTO credit_cards
VALUES
  (100, '4532993817514842'),
  (100, '4716002041425888'),
  (102, '5243112427642649'),
  (102, '6011720771834675'),
  (102, '6011378662059710'),
  (103, '373611968625635')
;

--run GRANT to grant permission to use the SELECT statement on the table
GRANT SELECT ON credit_cards TO PUBLIC;

--create two users
CREATE USER regular_user WITH PASSWORD '1234Test!';

CREATE USER analytics_user WITH PASSWORD '1234Test!';

--create the analytics_role role and grant it to analytics_user
--regular_user does not have a role
CREATE ROLE analytics_role;

GRANT ROLE analytics_role TO analytics_user;
```

Next, create a masking policy to apply to the analytics role\.

```
--create a masking policy that fully masks the credit card number
CREATE MASKING POLICY mask_credit_card_full
WITH (credit_card VARCHAR(256))
USING ('000000XXXX0000'::TEXT);

--create a user-defined function that partially obfuscates credit card data
CREATE FUNCTION REDACT_CREDIT_CARD (credit_card TEXT)
RETURNS TEXT IMMUTABLE
AS $$
    import re
    regexp = re.compile("^([0-9]{6})[0-9]{5,6}([0-9]{4})")
 
    match = regexp.search(credit_card)
    if match != None:
        first = match.group(1)
        last = match.group(2)
    else:
        first = "000000"
        last = "0000"
    
    return "{}XXXXX{}".format(first, last)
$$ LANGUAGE plpythonu;

--create a masking policy that applies the REDACT_CREDIT_CARD function
CREATE MASKING POLICY mask_credit_card_partial
WITH (credit_card VARCHAR(256))
USING (REDACT_CREDIT_CARD(credit_card));

--confirm the masking policies using the associated system views
SELECT * FROM svv_masking_policy;

SELECT * FROM svv_attached_masking_policy;
```

### Attaching a masking policy<a name="ddm-example-attach"></a>

Attach the masking policies to the credit card table\.

```
--attach mask_credit_card_full to the credit card table as the default policy
--all users will see this masking policy unless a higher priority masking policy is attached to them or their role
ATTACH MASKING POLICY mask_credit_card_full
ON credit_cards(credit_card)
TO PUBLIC;

--attach mask_credit_card_partial to the analytics role
--users with the analytics role can see partial credit card information
ATTACH MASKING POLICY mask_credit_card_partial
ON credit_cards(credit_card)
TO ROLE analytics_role
PRIORITY 10;

--confirm the masking policies are applied to the table and role in the associated system view
SELECT * FROM svv_attached_masking_policy;

--confirm the full masking policy is in place for normal users by selecting from the credit card table as regular_user
SET SESSION AUTHORIZATION regular_user;

SELECT * FROM credit_cards;

--confirm the partial masking policy is in place for users with the analytics role by selecting from the credit card table as analytics_user
SET SESSION AUTHORIZATION analytics_user;

SELECT * FROM credit_cards;
```

### Altering a masking policy<a name="ddm-example-alter"></a>

The following section shows how to alter a dynamic data masking policy\.

```
--reset session authorization to the default
RESET SESSION AUTHORIZATION;

--alter the mask_credit_card_full policy
ALTER MASKING POLICY mask_credit_card_full
USING ('00000000000000'::TEXT);	
	
--confirm the full masking policy is in place after altering the policy, and that results are altered from '000000XXXX0000' to '00000000000000'
SELECT * FROM credit_cards;
```

### Detaching and dropping a masking policy<a name="ddm-example-detach"></a>

The following section shows how to detach and drop masking policies by removing all dynamic data masking policies from the table\.

```
--reset session authorization to the default
RESET SESSION AUTHORIZATION;

--detach both masking policies from the credit_cards table
DETACH MASKING POLICY mask_credit_card_full 
ON credit_cards(credit_card) 
FROM PUBLIC;

DETACH MASKING POLICY mask_credit_card_partial 
ON credit_cards(credit_card) 
FROM ROLE analytics_role;

--drop both masking policies
DROP MASKING POLICY mask_credit_card_full;

DROP MASKING POLICY mask_credit_card_partial;
```

## Considerations when using dynamic data masking<a name="t_ddm-considerations"></a>

When using dynamic data masking, consider the following: 
+  When querying objects created from tables, such as views, users will see results based on their own masking policies, not the policies of the user who created the objects\. For example, a user with the analyst role querying a view created by a secadmin would see results with masking policies attached to the analyst role\. 
+  To prevent the EXPLAIN command from potentially exposing sensitive masking policy filters, only users with the SYS\_EXPLAIN\_DDM permission can see masking policies applied in EXPLAIN outputs\. Users don't have the SYS\_EXPLAIN\_DDM permission by default\.

  The following is the syntax for granting the permission to a user or role\.

  ```
  GRANT EXPLAIN MASKING TO { username | ROLE rolename }
  ```

   For more information on the EXPLAIN command, see [EXPLAIN](r_EXPLAIN.md)\. 
+  Users with different roles can see differing results based on the filter conditions or join conditions used\. For example, running a SELECT command on a table using a specific column value will fail if the user running the command has a masking policy applied that obfuscates that column\. 
+  DDM policies must be applied ahead of any predicate operations, or projections\. Masking polices may include the following:
  + Low cost constant operations such as converting a value to null
  + Moderate cost operations such as HMAC hashing
  + High cost operations such as calls to external Lambda user defined functions

  As such, we recommend that you use simple masking expressions when possible\. 
+  You can use DDM policies for roles with row\-level security policies, but note that RLS policies are applied before DDM\. A dynamic data masking expression will not be able to read a row that was protected by RLS\. For more information on RLS, see [Row\-level security](t_rls.md)\. 
+  When using the [COPY](r_COPY.md) command to copy from parquet to protected target tables, you should explicitly specify columns in the COPY statement\. For more information on mapping columns with COPY, see [Column mapping options](copy-parameters-column-mapping.md)\. 
+  DDM masking policies can attach to lookup tables\. They can't attach to the following relations, nor use them in the USING clause:
  +  System tables and catalogs 
  +  External tables 
  +  Datasharing tables 
  +  Views, materialized views, and late binding views 
  +  Cross\-DB relations 
  +  Temporary tables 
  +  Correlated queries 

  Following is an example of attaching a masking policy to a lookup table\.

  ```
  --Create a masking policy referencing a lookup table
  CREATE MASKING POLICY lookup_mask_credit_card WITH (credit_card TEXT) USING (
    CASE
      WHEN
        credit_card IN (SELECT credit_card_lookup FROM credit_cards_lookup)      
      THEN '000000XXXX0000'
      ELSE REDACT_CREDIT_CARD(credit_card)
      END
    ); 
    
  --Provides access to the lookup table via a policy attached to a role
  GRANT SELECT ON TABLE credit_cards_lookup TO MASKING POLICY lookup_mask_credit_card;
  ```
+  You can't attach a masking policy that would produce an output incompatible with the target column's type and size\. For example, you can’t attach a masking policy that outputs a 12 character long string to a VARCHAR\(10\) column\. Amazon Redshift supports the following exceptions: 
  +  A masking policy with input type INTN can be attached to a policy with size INTM as long as M < N\. For example, a BIGINT \(INT8\) input policy can be attached to a smallint \(INT4\) column\. 
  +  A masking policy with input type NUMERIC or DECIMAL can always be attached to a FLOAT column\. 