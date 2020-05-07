# datestyle<a name="r_datestyle"></a>

## Values \(default in bold\)<a name="t_Modifying_the_default_settings-values"></a>

 Format specification \(**ISO**, Postgres, SQL, or German\), and year/month/day ordering \(DMY, **MDY**, YMD\)\.

 **ISO, MDY** 

## Description<a name="description"></a>

Sets the display format for date and time values as well as the rules for interpreting ambiguous date input values\. The string contains two parameters that can be changed separately or together\.

## Example<a name="example"></a>

```
show datestyle;
DateStyle
-----------
ISO, MDY
(1 row)

set datestyle to 'SQL,DMY';
```