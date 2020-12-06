Databases
=========

Tables/collections
------------------

Tables (collections in non-relational stores) and indices generally store data on a one-file-per basis.
Because of this, you can get extremely high query performance when the entire index or table fits into RAM.
Indexes are particularly important to fit into RAM, since this is often where queries are run against.
You might then ask yourself, "why don't I just aggressively break my tables into tiny logical pieces? It should be easy to fit any particular table into RAM if they are small."

The first reason not to do this is because you lose the ability to do broad queries across all of your data.
When you have a single table, a search query can span across all rows in a single command.
However, if you break your tables into many smaller ones (say, one table per separate user account), you may have to do one query per table.
For similar reasons, this can complicate foreign keys and relational mappings across tables.

Another reason not to do this is dependent on your access patterns.
Say you have a 100GB data set, and you split it into 100000 1MB tables.
If you nearly always have to query across all 100000 tables, you've actually made your system slower, because 1) you have to do 100000 separate queries 2) that's not going to fit in RAM anyway, and you will be constantly evicting data from your cache.
However, if you generally only query the same 20 tables, and the rest are rarely accessed, your effective working set is only 20MB, and you've significantly improved your performance.
This sort of behavior is common if you keep time series data and only access the most recent results, or if you have certain hotspot data.
Intelligent breaking up of tables is critically important here.

As a rule, don't break your system up into too many tables, as it becomes difficult to manage, and don't prematurely optimize things.
If your data set is always going to remain small, it's better to just leave things as a unified set.


Columnar databases
------------------

Databases traditionally store data row-by-row, where if you write a few rows of data, the row is stored sequentially on disk.
You can also store data column-by-column, where if you write a few rows of data, the columns are grouped together.
Take a database that stores name-count pairings, for instance.
This is how the data set would look on disk for the two methods of storage:

Row-based:

matt|3|jack|4|paul|5|dudley|6

Column-based:

matt|jack|paul|dudley|3|4|5|6

This has a ton of implications, the first of which is how disk paging works here.
Say you read a bunch of records from just one of the columns.
In row-based, you will have to jump to many locations on the disk, since the data for that one column is all over the place.
In column-based, you don't have to jump around as much on the drive since the data is grouped together, and repeated access will be able to use page-cached data.
The inverse is true if you are using data from most/all of the columns.

Columnar databases also tend to be able to compress their data better, as the data is typically more similar than in row-based data.
Columnar databases are not great at writing data incrementally, as it may have to jump all over the place on-disk.

Via https://www.flydata.com/blog/whats-unique-about-a-columnar-database/

```
To summarize, columnar databases are good for:

    Queries that involve only a few columns
    Aggregation queries against vast amounts of data
    Column-wise compression

But are not so good at:

    Queries against only a few rows
    Incremental data loading
    Online Transaction Processing (OLTP) usage
```

Rules of thumb
--------------

http://mysql.rjweb.org/doc.php/ricksrots

- If more than 20% of the rows are needed, a table scan is faster than using the obvious INDEX. (This RoT usually explains why "the optimizer did not use my index".)
- Often "compound indexes" (multiple columns in a single INDEX(...)) are better than single-column indexes.
- Perhaps the most common use case where PARTITIONing shines is in a the dataset where "old" data is deleted from the table periodically.
- InnoDB is faster than MyISAM -- contradicts the 'old' wisdom; InnoDB has improved.
- Use InnoDB instead of MyISAM. MyISAM is going away, not supported, fragile, etc.
- If more than 20% of the rows are needed, a table scan is faster than using the obvious INDEX. (This RoT usually explains why "the optimizer did not use my index".)
- Use 100-1000 rows per batch INSERT or DELETE (10x speedup).
- Rows clustered together are 10x faster to access than random rows (fewer blocks to fetch).
- How seriously to take optimization? 1K rows: yawn; 1M rows; serious; 1B rows: all of these tips, plus more.
- Don't let a BEGIN...COMMIT last more than a few seconds.
- Turn off the Query Cache: query_cache_type=off and query_cache_size=0.
- Plain disks can handle 100 reads/writes per second; SSDs: 1000/sec.
