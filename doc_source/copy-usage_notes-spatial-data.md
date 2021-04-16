# Loading a column of the GEOMETRY data type<a name="copy-usage_notes-spatial-data"></a>

You can only COPY to `GEOMETRY` columns from data in text or CSV format\. The data must be in the hexadecimal form of the extended well\-known binary \(EWKB\) format and fit within the maximum size of a single input row to the COPY command\. For more information, see [COPY](r_COPY.md)\. 