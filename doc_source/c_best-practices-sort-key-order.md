# Load Data in Sort Key Order<a name="c_best-practices-sort-key-order"></a>

Load your data in sort key order to avoid needing to vacuum\.

As long as each batch of new data follows the existing rows in your table, your data will be properly stored in sort order, and you will not need to run a vacuum\. You don't need to presort the rows in each load because COPY sorts each batch of incoming data as it loads\.

For example, suppose you load data every day based on the current day's activity\. If your sort key is a timestamp column, your data is stored in sort order because the current day's data is always appended at the end of the previous day's data\. For more information, see [Loading Your Data in Sort Key Order](vacuum-load-in-sort-key-order.md)