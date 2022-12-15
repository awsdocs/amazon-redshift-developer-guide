# Masking policy hierarchy<a name="t_ddm-hierarchy"></a>
+ You can attach multiple masking policies to a single column\.
+ When multiple masking policies are applicable to a query, the highest priority policy attached to each respective column applies\. Consider the following example\. 

  ```
  ATTACH MASKING POLICY partial_hash
  ON credit_cards(address, credit_card)
  TO ROLE analytics_role 
  PRIORITY 20;
  
  ATTACH MASKING POLICY full_hash
  ON credit_cards(credit_card, ssn)
  TO ROLE auditor_role 
  PRIORITY 30;
  
  SELECT address, credit_card, ssn
  FROM credit_cards;
  ```

  When running the SELECT statement, a user with both the analytics and auditor roles sees the address column with the `partial_hash` masking policy applied\. They see the credit card and SSN columns with the `full_hash` masking policy applied, because the `full_hash` policy has the higher priority on the credit card column\.
+  If you don't specify a priority when attaching a masking policy, the default priority is 0\. 
+ You can't attach two policies to the same column with equal priority\. 
+ You can't attach two policies to the same column/role pair\.

To confirm which masking policy applies to a given role/user and column combination, users with the [https://docs.aws.amazon.com/redshift/latest/dg/r_roles-default.html](https://docs.aws.amazon.com/redshift/latest/dg/r_roles-default.html) role can look up the column/role or column/user pair in the [SVV\_ATTACHED\_MASKING\_POLICY](r_SVV_ATTACHED_MASKING_POLICY.md) system view\. For more information, see [System views for dynamic data masking](r_ddm-svv.md)\.