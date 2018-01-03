# END<a name="r_END"></a>

Commits the current transaction\. Performs exactly the same function as the COMMIT command\.

See [COMMIT](r_COMMIT.md) for more detailed documentation\.

## Syntax<a name="r_END-synopsis"></a>

```
END [ WORK | TRANSACTION ]
```

## Parameters<a name="r_END-parameters"></a>

WORK  
Optional keyword\.

TRANSACTION  
Optional keyword; WORK and TRANSACTION are synonyms\.

## Examples<a name="r_END-examples"></a>

The following examples all end the transaction block and commit the transaction:

```
end;
```

```
end work;
```

```
end transaction;
```

After any of these commands, Amazon Redshift ends the transaction block and commits the changes\.