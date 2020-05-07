# Example: Choosing compression encodings for the CUSTOMER table<a name="Examples__compression_encodings_in_CREATE_TABLE_statements"></a>

The following statement creates a CUSTOMER table that has columns with various data types\. This CREATE TABLE statement shows one of many possible combinations of compression encodings for these columns\. 

```
create table customer(
custkey int encode delta,
custname varchar(30) encode raw,
gender varchar(7) encode text255,
address varchar(200) encode text255,
city varchar(30) encode text255,
state char(2) encode raw,
zipcode char(5) encode bytedict,
start_date date encode delta32k);
```

The following table shows the column encodings that were chosen for the CUSTOMER table and gives an explanation for the choices:


| Column | Data type | Encoding | Explanation | 
| --- | --- | --- | --- | 
| CUSTKEY | int | delta | CUSTKEY consists of unique, consecutive integer values\. Since the differences will be one byte, DELTA is a good choice\. | 
| CUSTNAME | varchar\(30\) | raw | CUSTNAME has a large domain with few repeated values\. Any compression encoding would probably be ineffective\. | 
| GENDER | varchar\(7\) | text255 | GENDER is very small domain with many repeated values\. Text255 works well with VARCHAR columns in which the same words recur\. | 
| ADDRESS | varchar\(200\) | text255 | ADDRESS is a large domain, but contains many repeated words, such as Street Avenue, North, South, and so on\. Text 255 and text 32k are useful for compressing VARCHAR columns in which the same words recur\. The column length is short, so text255 is a good choice\. | 
| CITY | varchar\(30\) | text255 | CITY is a large domain, with some repeated values\. Certain city names are used much more commonly than others\. Text255 is a good choice for the same reasons as ADDRESS\. | 
| STATE | char\(2\) | raw | In the United States, STATE is a precise domain of 50 two\-character values\. Bytedict encoding would yield some compression, but because the column size is only two characters, compression might not be worth the overhead of uncompressing the data\. | 
| ZIPCODE | char\(5\) | bytedict | ZIPCODE is a known domain of fewer than 50,000 unique values\. Certain zip codes occur much more commonly than others\. Bytedict encoding is very effective when a column contains a limited number of unique values\.  | 
| START\_DATE | date | delta32k | Delta encodings are very useful for datetime columns, especially if the rows are loaded in date order\. | 