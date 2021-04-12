title: 《Apache Hive Cookbook》读书笔记
author: Jiezhi.G
email: Jiezhi.G@gmail.com
tags:
  - Note
  - Hive
  - Big Data
categories:
  - Hive
premalink: fix_gravatar
metaAlignment: center
comments: true
date: 2019-02-19 17:26:42
url:
photos:

---

有 SQL 基础的话，差不多一天可以看完。
这里把我看这本书做的笔记分享一下（从 [Notion](https://www.notion.so/?r=507f808e5cb544809dfe98d930ee2cae) 里迁移出来）

<!--more-->
{% asset_img cover.jpg cover %}
# Developing Hive

## Deploying Hive Metastore

- The Hive table and database definitions and mapping to the data in HDFS is stored in a metastore.
- A metastore is a central repository for Hive metadata. Consists two main components:
    - Services to which the client connects and queries the metastore
    - A backing database to store the metadata
- Configure:
    - An **embedded** metastore
    - A **local** metastore
    - A **remote** metastore
    {% asset_img 1.png hive-service %}

    <!-- ![](/hive-cookbook-hive-service.png) -->

    HIVE Service JVM

# Services in Hive

## HiveServer2

> HiveServer2 is an enhancement of HiveServer provided in earlier versions of Hive.HiveServer's limitations of **concurrency** and **authentication** is resolved in HiveServer2. HiveServer2 is based on **Thrift RPC**.It supports multiple types of clients, including JDBC and ODBC.

## HiveServer2 High Availability

- **ZooKeeper**

## Hive metastore service

- In Hive, the data is stored in **HDFS** and the table, database, schema, and the HQL definitions are stored in a **metastore**.
- The metastore could be any RDBMS database, such as MYSQL or Oracle.
- Hive creates a database and a set of tables in metastore to store HiveQL definitions.

## Hue

# Understanding the Hive Data Model

## Intruduction

- Data types
    - Primitive data types
        - Numeric data types
        - String data types
        - Date/Time data type
        - Miscellaneous data types
            - Boolean
            - Binary
    - Complex data types
        - STRUCT
        - MAP
        - ARRAY

## Partitioning

> Partitioning in Hive is used to increase query performance.

- **Mnaged** table

    > In a managed table, if you delete a talbe, then the data of that table will also get deleted. Similarly, if you delete a partition, then the data of that partition will also get deleted.

        CREATE TABLE customer(id STRING, name STRING, gender STRING) 
        PARTITIONED BY (country STRING, state STRING);

    - By setting the value of `hive.mapred.mode` to strict, it will prevent running risky queries.
    - Loading data in a managed partitioned table
        - Static Partitioning
        - Dynamic Partitioning
- **External** table

    > Patitioning external tables works in the same way as in managed tables. Except this in the external table, when you delete a partition, the data file doesn't get deleted.

## Bucketing

> Bucketing is a technique that allows you to decompose your data into more manageable parts, that is, fix the number of buckets.

- Usually, partitioning provides a way of segregating the data of a Hive table into multiple files or directories.
- Partitioning doesn't perform well if there is a large number of partitions.
- Bucketing concept is based on the hashing principle, where same type of keys are always sent to the same bucket.

    `Bucket number = hash_function(bucketing_column) mod num_buckets`

    `set hive.enforce.bucketing=true;`

- Two bullet points:
    - In partitioning, a column defined as a partitioned column is not included in a schema columns of a Hive table. But in bucketing, a column defined as a bucketed column is included in the schema columns of the Hive table.
    - We cannot use the `LOAD DATA` statement to load the data into the bucketed table as we do in partitioned table. Rather, we have to use the `INSERT` statements to insert data by selecting data from some other table.

    # Hive Data Definition Language(DDL)

    ## Creating tables

    - The `LIKE` clause in a create table command creates a copy of an existing table with a different name and without the data. It just creates a structure like that of an existing table without copying its data.
    - Parameters:
        - [TEMPORARY]
        - [EXTERNAL]
        - [IF NOT EXISTS]
        - [PARTITIONED BY]
        - [CLUSTERED BY]
        - [SKEWED BY]: 解决数据倾斜问题，较多的值被分隔成多个文件，其余的值分到其他文件中。
        - ...

    ## Creating views

    > A `view` is a virtual table that acts as a window to the dat for the underlying table commonly known as the `base` table. It consists of rows and columns but no physical data. So when a `view` is accessed, the underlying `base` table is queried for the output.

## HCatalog

> `HCatalog` is a storage management tool that enables framworks other than Hive to leverage a data model to read and write data. HCatalog tables provide an abstraction on the data format in HDFS and allow frameworks such as `PIG` and `MapReduce` to use the data without being concerned about the data format, such as `RC`, `ORC`, and text files.

- `HCatInputFormat` and `HCatOutputFormat`, which are the implementations of Hadoop `InputFormat` and `OutputFormat`, are the interfaces provided to `PIG` and `MapReduce`.

## WebHCat

> WebHCat, formerly called Templeton, allow access to the HCatalog service using REST APIs.

# Hive Data Manipulation Language(DML)

# Hive Extensibility Features

## Serialization and deserialization formats and data types

- LazySimpleSerDe, the default SerDes format of Hive.
- RegexSerDe
- AvroSerDe
- OrcSerDe
- ParquetHiveSerDe
- JSONSerDe
- CSVSerDe

## Exploring views

- A view is treated as a table in Hive

## Exploring indexes

- Indexes are useful for increasing the performance of frequent queries based on certain columns.

## Hive partitioning

- Static partitioning
- Dynamic partitioning

## Creating buckets in Hive

    CREATE table sales_buck (id int, fname string, ...) 
    clustered by (id) into 50 buckets row format delimited fields
    terminated by '\t';
    
    set hive.enforce.bucketing=true;
    
    insert into table sales_buck select * from sales;

## Analytics functions in Hive

*see page 122*

- RANK

    > It is similar to ROW_NUMBER, but the equal rows are ranked with the same number.

- DENSE_RANK

    > In a normal RANK function, we see a gap between the numbers in rows. DENSE_RANK is a function with no gap.

- ROW_NUMBER

    > This function will provide a unique number to each row in resultset based on the ORDER BY clause within the PARTITION.

- PERCENT_RANK

    > It is very similar to the CUME_DIST function. It returns a value from 0 to 1 inclusive.

- CUME_DIST(Cumulative distribution)

    > It computes the relative postion of a column value in a group.

- NTILE

    > NTILE distributes the number of rows in a partition into a certain number of groups.

## Windowing in Hive

*see page 126*

> Windowing in Hive allows an analyst to creagte a window of data to operate aggregation and other analytical functions, such as LEAD and LAG.

- Partition specification

    It includes a column reference from the table. It could not be any aggregation or other window specification.

- Order specification

    It comprises a combination of one or more columns.

    - Handling NULLs

        There is no support for Nulls first or last specification. In Hive, Nulls are returned first.

- Window frame

    A frame has a start boundary and an optional end boundary:

    - Frame Type
        - ROW
        - RANGE
- Frame boundary
- Effective window frames
- Source name for window definition
- LEAD

    > The LEAD function is used to return the data from the next set of rows.

- LAG

    > The LAG function is used to return the data from the previous set of rows.

- FIRST_VALUE
- LAST_VALUE

## File formats

- TEXTFILE, default format
- SEQUENCEFILE

    If you want to save disk storage while keeping large datasets

- RCFILE

    Also known as **Record Columnar File**, stores data in a compressed format on the disk. It provides the following features of storage and processing optimization:

    - Fast storage of data
    - Optimized storage utilization
    - Better query processing

    The RCFILE format flattens the data in terms of both rows and columns. If you need a certain column for analytics, it would not scan the complete data; instead, it would return the required columns.

- ORC

    **Optimized Row Columnar**

    This is a highly efficient way of storing and processing data in Hive. Data stored in the ORC format improves performance in reading, writing, and processing data with Hive.

- PARQUET

    This is a column-oriented storage format that is efficient at querying particular columns in the table.

- AVRO

# Joins and Join Optimization

## Using a skew join

A skew join is used when there is a table with skew data in the joining column. A skew table is a table that is having values that are present in large numbers in the table compared to other data. Skew data is stored in a separate file while the rest of the data is stored in a separate file. 

If there is a need to perform a join on a column of a table that is appearing quite often in the table, the data for that particular column will go to a single reducer, which will become a bottleneck while performing the join. To reduce this, a skew join is used.

# Statics in Hive

## Analyze

    ANALYZE TABLE sales COMPUTE STATISTICS;

# Functions in Hive

# Hive Tuning

## Enabling predicate pushdown optimiztions in Hive

Predicate pushdown is a traditional RDBMS term, whereas in Hive, it works as predicate pushup.

`hvie.optimize.ppd=true;`

## Optimizations to reduce the number of map

## Sampling

> Sampling in Hive is a way to wirte queries on a small chunk of data instead of the entire table.

# Hive Security

# Hive Integration with Other Frameworks

## Working with Apache Spark

## Working with Accumulo

## Working with HBase (Google Drill)


