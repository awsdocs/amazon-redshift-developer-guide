# COMMIT<a name="r_COMMIT"></a>

Commits the current transaction to the database\. This command makes the database updates from the transaction permanent\.

## Syntax<a name="r_COMMIT-synopsis"></a>

```
COMMIT [ WORK | TRANSACTION ]
```

## Parameters<a name="r_COMMIT-parameters"></a>

WORK  
Optional keyword\.

TRANSACTION  
Optional keyword; WORK and TRANSACTION are synonyms\.

## Examples<a name="r_COMMIT-examples"></a>

Each of the following examples commits the current transaction to the database:

```
commit;
```

```
commit work;
```

```
commit transaction;
```