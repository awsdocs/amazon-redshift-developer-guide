# enable\_case\_sensitive\_identifier<a name="r_enable_case_sensitive_identifier"></a>

## Values \(default in bold\)<a name="r_enable_case_sensitive_identifier-values"></a>

true, **false**

### Description<a name="description"></a>

A configuration value that determines whether name identifiers of databases, tables, and columns are case sensitive\. The case of name identifiers is preserved when enclosed within double quotation marks\. When you set `enable_case_sensitive_identifier` to `true`, the case of name identifiers is preserved\. When you set `enable_case_sensitive_identifier` to `false`, the case of name identifiers is not preserved\. 

The case of a *username* enclosed in double quotation marks is always preserved regardless of the setting of the `enable_case_sensitive_identifier` configuration option\.

### Examples<a name="w166aac68c45b3b7"></a>

The following example shows how to create and use case sensitive identifiers for at table and column name\.

```
-- To create and use case sensitive identifiers
SET enable_case_sensitive_identifier TO true;
        
-- Create tables and columns with case sensitive identifiers
CREATE TABLE "MixedCasedTable" ("MixedCasedColumn" int);
            
CREATE TABLE MixedCasedTable (MixedCasedColumn int);

-- Now query with case sensitive identifiers
SELECT "MixedCasedColumn" FROM "MixedCasedTable";           

MixedCasedColumn
------------------
(0 rows)

            
SELECT MixedCasedColumn FROM MixedCasedTable;            

mixedcasedcolumn
------------------
(0 rows)
```

The following example shows when the case of identifiers is not preserved\.

```
-- To not use case sensitive identifiers
RESET enable_case_sensitive_identifier;

-- Mixed case identifiers are lowercased 
CREATE TABLE "MixedCasedTable2" ("MixedCasedColumn" int);

CREATE TABLE MixedCasedTable2 (MixedCasedColumn int);

ERROR:  Relation "mixedcasedtable2" already exists


SELECT "MixedCasedColumn" FROM "MixedCasedTable2";

 mixedcasedcolumn
------------------
(0 rows)


SELECT MixedCasedColumn FROM MixedCasedTable2;

 mixedcasedcolumn
------------------
(0 rows)
```