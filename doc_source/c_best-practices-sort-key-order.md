# Load data in sort key order<a name="c_best-practices-sort-key-order"></a>

Load your data in sort key order to avoid needing to vacuum\.

If each batch of new data follows the existing rows in your table, your data is properly stored in sort order, and you don't need to run a vacuum\. You don't need to presort the rows in each load because COPY sorts each batch of incoming data as it loads\.

For example, suppose that you load data every day based on the current day's activity\. If your sort key is a timestamp column, your data is stored in sort order\. This order occurs because the current day's data is always appended at the end of the previous day's data\. For more information, see [Loading your data in sort key order](vacuum-load-in-sort-key-order.md)\.