---
title: 'Write-Ahead Logging(WAL)'
weight: 1
references:
    links:
        - https://www.crunchydata.com/blog/postgres-wal-files-and-sequuence-numbers
        - https://hevodata.com/learn/working-with-postgres-wal
--- 

> The Postgres Write Ahead Log (WAL) is a functional component to the database. WAL makes a lot of key functionality possible, like Point-in-Time-Recovery backups, recovering from an event, streaming replication, and more. From time to time, those deep inside the database will need to work directly with WAL files to diagnose or recover.

# Log Sequence Number

Transactions in PostgreSQL create WAL records which are ultimately appended to the WAL log (file). The position where the insert occurs is known as the Log Sequence Number (LSN). The values of LSN (of type pg_lsn) can be compared to determine the amount of WAL generated between two different offsets (in bytes). When using in this manner, it is important to know the calculation assumes the full WAL segment was used (16MB) if multiple WAL logs are used. A similar calculation to the one used here is often used to determine latency of a replica.

The LSN is a 64-bit integer, representing a position in the write-ahead log stream. This 64-bit integer is split into two segments (high 32 bits and low 32 bits). It is printed as two hexadecimal numbers separated by a slash (XXXXXXXX/YYZZZZZZ). The 'X' represents the high 32-bits of the LSN and ‘Y’ is the high 8 bits of the lower 32-bits section. The 'Z' represents the offset position in the file. Each element is a hexadecimal number. The 'X' and 'Y' values are used in the second part of the WAL file on a default PostgreSQL deployment.

```sql
select pg_current_wal_lsn(), pg_current_wal_insert_lsn()
```

# WAL File
The WAL file name is in the format TTTTTTTTXXXXXXXXYYYYYYYY. Here 'T' is the timeline, 'X' is the high 32-bits from the LSN, and 'Y' is the low 32-bits of the LSN.

Start by looking at the current WAL LSN and insert LSN. The pg_current_wal_lsn is the location of the last write. The pg_current_wal_insert_lsn is the logical location and reflects data in the buffer that has not been written to disk. There is also a flush value that shows what has been written to durable storage.


```sql
select pg_walfile_name(pg_current_wal_insert_lsn())
```


# WAL Configuration
Postgres WAL configuration parameters affect how the database performs. Two subsystems: checkpointer and background writer are very important in the context of PostgreSQL WAL. Let us first look into some memory area concepts in the RAM to understand where those subsystems operate and what their function is.

Here is what you will cover:

1. Shared Buffers
2. Dirty Pages
3. Checkpointer
4. Background Writer

## Shared Buffers
Inside the postgresql.conf file, there is a parameter called shared_buffers. They are called ‘shared’ buffers because all of the background servers can access them. This parameter determines the amount of memory allocated to PostgreSQL for caching data. Dealing with memory is faster than reading from or writing to the files and hence it improves performance. A back-end process requests the buffer manager for a page and the buffer manager either loads the required page from persistent storage or returns it from the buffer pool if it is available. Now the back-end process can access the page.

## Dirty Pages
When data is modified in memory, the page in which this data is stored is called a ‘dirty page’, as long as the modifications are not written to disk. So if there is a page that is different in the shared buffer and the disk, it is called a dirty page. The buffer manager flushes the dirty pages to storage with the assistance of two subsystems called – checkpointer and background writer.

## Checkpointer
As the name suggests, checkpointer creates checkpoints in the Write-Ahead Log. These checkpoints are used for recovery. A checkpoint indicates that all the information prior to the checkpoint has been updated. So at every checkpoint, dirty pages are flushed to disk. How often a checkpoint is begun depends on checkpoint_segments and checkpoint_timeout.

The integer, checkpoint_segments indicates the maximum number of log segments between two checkpoints. The default value is 3 segments, where each segment is usually 16 MB. This value can be adjusted in the postgresql.conf file.

On the other hand, checkpoint_timeout is an integer that indicates the maximum time between two checkpoints. The default time is 5 minutes/ 300 seconds. This value can also be adjusted in the postgresql.conf file.

Since there are two parameters, a checkpoint is created when any one of the default conditions is met.

The file, postgresql.conf can be edited to increase the frequency of checkpoints, by reducing the checkpoint_segments and the checkpoint_timeout parameters. By reducing the integer value of these parameters, you will be flushing dirty pages more frequently, which is not desirable because checkpoints are costly. But on the other hand, after a crash, the recovery will be quicker.

In the case of bulk operations, you might have to adjust a parameter called checkpoint_warning, which will warn you when the checkpoints are too frequent. Taking a cue from the warnings, you can increase the checkpoint_segments value.

## Background Writer
The background writer is a subsystem that assists the checkpointer. What it does is, move some of the modified data to the disk offloading the checkpointer. You should however be careful before tuning it as it increases the I/O load, especially in cases where there is some page that is being dirtied repeatedly. In such a case the background writer would write it repeatedly within a checkpoint interval.

