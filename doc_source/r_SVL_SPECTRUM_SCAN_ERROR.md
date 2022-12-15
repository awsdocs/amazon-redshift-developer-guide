# SVL\_SPECTRUM\_SCAN\_ERROR<a name="r_SVL_SPECTRUM_SCAN_ERROR"></a>

You can query the system view SVL\_SPECTRUM\_SCAN\_ERROR to get information about Redshift Spectrum scan errors\. 

SVL\_SPECTRUM\_SCAN\_ERROR is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_SVL_SPECTRUM_SCAN_ERROR-table-columns"></a>

Displays a sample of logged errors\. The default is 10 entries per query\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_SPECTRUM_SCAN_ERROR.html)

## Sample query<a name="r_SVL_SPECTRUM_SCAN_ERROR-sample-query"></a>

The following query returns the list of rows for which data handling operations were performed\. 

```
SELECT * FROM svl_spectrum_scan_error;
```

The query returns results similar to the following\. 

```
   userid  query     location                                      rowid   colname               original_value             modified_value       trigger          action                        action_valueerror_code
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:0     league_name           Barclays Premier League    Barclays Premier Lea UNSPECIFIED      TRUNCATE                                          156
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:0     league_nspi           34595                      32767                UNSPECIFIED      OVERFLOW_VALUE                                    199
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:1     league_nspi           34151                      32767                UNSPECIFIED      OVERFLOW_VALUE                                    199
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:2     league_name           Barclays Premier League    Barclays Premier Lea UNSPECIFIED      TRUNCATE                                          156
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:2     league_nspi           33223                      32767                UNSPECIFIED      OVERFLOW_VALUE                                    199
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:3     league_name           Barclays Premier League    Barclays Premier Lea UNSPECIFIED      TRUNCATE                                          156
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:3     league_nspi           32808                      32767                UNSPECIFIED      OVERFLOW_VALUE                                    199
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:4     league_nspi           32790                      32767                UNSPECIFIED      OVERFLOW_VALUE                                    199
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:5     league_name           Spanish Primera Division   Spanish Primera Divi UNSPECIFIED      TRUNCATE                                          156
     100   1574007   s3://spectrum-uddh/league/spi_global_rankings.0:6     league_name           Spanish Primera Division   Spanish Primera Divi UNSPECIFIED      TRUNCATE                                          156
```