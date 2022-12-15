# Managing dynamic data masking policies<a name="r_ddm-procedures"></a>

You can perform the following actions:
+ To create a DDM policy, use the [CREATE MASKING POLICY](r_CREATE_MASKING_POLICY.md) command\.

  The following is an example of creating a masking policy using a SHA\-2 hash function\.

  ```
  CREATE MASKING POLICY hash_credit 
  WITH (credit_card varchar(256)) 
  USING (sha2(credit_card + 'testSalt', 256));
  ```
+ To attach a DDM policy on a table to one or more users or roles, use the [ATTACH MASKING POLICY](r_ATTACH_MASKING_POLICY.md) command\.

  The following is an example of attaching a masking policy to a column/role pair\.

  ```
   
  ATTACH MASKING POLICY hash_credit 
  ON credit_cards (credit_card) 
  TO ROLE science_role 
  PRIORITY 30;
  ```

  The PRIORITY clause determines which masking policy applies to a user session when multiple policies are attached to the same column\. For example, if the user in the preceding example has another masking policy attached to the same credit card column with a priority of 20, science\_role's policy is the one that applies, as it has the higher priority of 30\.
+ To detach a DDM policy on a table from one or more users or roles, use the [DETACH MASKING POLICY](r_DETACH_MASKING_POLICY.md) command\.

  The following is an example of detaching a masking policy from a column/role pair\.

  ```
  DETACH MASKING POLICY hash_credit 
  ON credit_cards(credit_card) 
  FROM ROLE science_role;
  ```
+ To drop a DDM policy from all databases, use the [DROP MASKING POLICY](r_DROP_MASKING_POLICY.md) command\.

  The following is an example of dropping a masking policy from all databases\.

  ```
  DROP MASKING POLICY hash_credit;                  
  ```