# TRANSLATE function<a name="r_TRANSLATE"></a>

For a given expression, replaces all occurrences of specified characters with specified substitutes\. Existing characters are mapped to replacement characters by their positions in the *characters\_to\_replace* and *characters\_to\_substitute* arguments\. If more characters are specified in the *characters\_to\_replace* argument than in the *characters\_to\_substitute* argument, the extra characters from the *characters\_to\_replace* argument are omitted in the return value\.

TRANSLATE is similar to the [REPLACE function](r_REPLACE.md) and the [REGEXP\_REPLACE function](REGEXP_REPLACE.md), except that REPLACE substitutes one entire string with another string and REGEXP\_REPLACE lets you search a string for a regular expression pattern, while TRANSLATE makes multiple single\-character substitutions\.

If any argument is null, the return is NULL\.

## Syntax<a name="r_TRANSLATE-synopsis"></a>

```
TRANSLATE ( expression, characters_to_replace, characters_to_substitute )
```

## Arguments<a name="r_TRANSLATE-arguments"></a>

 *expression*   
The expression to be translated\.

 *characters\_to\_replace*   
A string containing the characters to be replaced\.

 *characters\_to\_substitute*   
A string containing the characters to substitute\.

## Return type<a name="r_TRANSLATE-return-type"></a>

VARCHAR

## Examples<a name="r_TRANSLATE-examples"></a>

The following example replaces several characters in a string: 

```
select translate('mint tea', 'inea', 'osin');

translate
-----------
most tin
```

The following example replaces the at sign \(@\) with a period for all values in a column: 

```
select email, translate(email, '@', '.') as obfuscated_email
from users limit 10;

email                                           obfuscated_email
-------------------------------------------------------------------------------------------
Etiam.laoreet.libero@sodalesMaurisblandit.edu   Etiam.laoreet.libero.sodalesMaurisblandit.edu
amet.faucibus.ut@condimentumegetvolutpat.ca     amet.faucibus.ut.condimentumegetvolutpat.ca
turpis@accumsanlaoreet.org	                turpis.accumsanlaoreet.org
ullamcorper.nisl@Cras.edu	                ullamcorper.nisl.Cras.edu
arcu.Curabitur@senectusetnetus.com              arcu.Curabitur.senectusetnetus.com
ac@velit.ca	                                ac.velit.ca
Aliquam.vulputate.ullamcorper@amalesuada.org    Aliquam.vulputate.ullamcorper.amalesuada.org
vel.est@velitegestas.edu                        vel.est.velitegestas.edu
dolor.nonummy@ipsumdolorsit.ca                  dolor.nonummy.ipsumdolorsit.ca
et@Nunclaoreet.ca                               et.Nunclaoreet.ca
```

 The following example replaces spaces with underscores and strips out periods for all values in a column: 

```
select city, translate(city, ' .', '_') from users
where city like 'Sain%' or city like 'St%'
group by city
order by city;

city            translate
--------------+------------------
Saint Albans     Saint_Albans
Saint Cloud      Saint_Cloud
Saint Joseph     Saint_Joseph
Saint Louis      Saint_Louis
Saint Paul       Saint_Paul
St. George       St_George
St. Marys        St_Marys
St. Petersburg   St_Petersburg
Stafford         Stafford
Stamford         Stamford
Stanton          Stanton
Starkville       Starkville
Statesboro       Statesboro
Staunton         Staunton
Steubenville     Steubenville
Stevens Point    Stevens_Point
Stillwater       Stillwater
Stockton         Stockton
Sturgis          Sturgis
```