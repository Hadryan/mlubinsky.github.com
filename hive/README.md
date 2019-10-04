## Architecture

<http://gethue.com/>  SQL Web client

<https://github.com/dropbox/PyHive> Python client for Hive and Presto

<https://www.adaltas.com/en/2019/07/25/hive-3-features-tips-tricks/>

<https://www.adaltas.com/en/2019/06/17/druid-hive-integration/>


Metastore (usually MySQL) stores the table definition
/etc/hive/conf/
hive-site.xml

DROP DATABASE x
DROP TABLE a -- before DROP DATABASE x
<https://www.youtube.com/watch?v=vwac18EzGGs> . Hive Table dissected
<https://www.youtube.com/playlist?list=PLOaKckrtCtNvLuuSkDdx71hAhPyNSqf66>
<https://www.youtube.com/watch?v=dwd9m1Zl04Q> . Hive JOIN OPTIMIZATION

Shuffling is expensive
Hints
/* +STREAMTABLE */
/* +MAPJOIN */

<https://community.hortonworks.com/articles/149894/llap-a-one-page-architecture-overview.html>


SMB (sort merge backeted)  MAP JOIN

Hive metastore. To enable the usage of Hive metastore outside of Hive, a separate project called HCatalog was started. 
  HCatalog is a part of Hive and serves the very important purpose of allowing other tools (like Pig and MapReduce)
  to integrate with the Hive metastore.

 Physically, a partition in Hive is nothing but just a sub-directory in the table directory.
CREATE TABLE table_name (column1 data_type, column2 data_type) 
PARTITIONED BY (partition1 data_type, partition2 data_type,….);
 
Partitioning is works better when the cardinality of the partitioning field is not too high .

<https://stackoverflow.com/questions/19128940/what-is-the-difference-between-partitioning-and-bucketing-a-table-in-hive>
<http://www.hadooptpoint.org/difference-between-partitioning-and-bucketing-in-hive/>

Clustering aka bucketing on the other hand, will result with a fixed number of files, 
since you do specify the number of buckets. 
What Hive will do is to take the field, calculate a hash and assign a record to that bucket.
But what happens if you use let's say 256 buckets and the field you're bucketing on has a low cardinality 
(for instance, it's a US state, so can be only 50 different values) ? 
You'll have 50 buckets with data, and 206 buckets with no data.

CREATE TABLE table_name PARTITIONED BY (partition1 data_type, partition2 data_type,….) 
CLUSTERED BY (column_name1, column_name2, …) 
SORTED BY (column_name [ASC|DESC], …)] 
INTO num_buckets BUCKETS;

Partitions can dramatically cut the amount of data you're querying.
 if you want to query only from a certain date forward, the partitioning by year/month/day is going to dramatically cut the amount of IO.
bucketing can speed up joins with other tables that have exactly the same bucketing, 
 if you're joining two tables on the same employee_id, hive can do the join bucket by bucket
 (even better if they're already sorted by employee_id since it's going to to a mergesort which works in linear time).

So, bucketing works well when the field has high cardinality and data is evenly distributed among buckets. 
Partitioning works best when the cardinality of the partitioning field is not too high.

Also, you can partition on multiple fields, with an order (year/month/day is a good example),
while you can bucket on only one field.


## Hive 3 streaming

Hive HCatalog Streaming API
Traditionally adding new data into Hive requires gathering a large amount of data onto HDFS and then periodically adding a new partition. This is essentially a “batch insertion”. Insertion of new data into an existing partition is not permitted. Hive Streaming API allows data to be pumped continuously into Hive. The incoming data can be continuously committed in small batches of records into an existing Hive partition or table. Once data is committed it becomes immediately visible to all Hive queries initiated subsequently.

hive-site.xml to enable ACID support for streaming:
hive.txn.manager = org.apache.hadoop.hive.ql.lockmgr.DbTxnManager
hive.compactor.initiator.on = true (See more important details here)
hive.compactor.worker.threads > 0 

## Cost based optimizer Calcite 
<https://cwiki.apache.org/confluence/display/Hive/Cost-based+optimization+in+Hive>

some of optimization decisions that can benefit from a CBO:

How to order Join
What algorithm to use for a given Join
Should the intermediate result be persisted or should it be recomputed on operator failure.
The degree of parallelism at any operator (specifically number of reducers to use).
Semi Join selection

## Vectorization

Vectorization allows Hive to process a batch of rows together instead of processing one row at a time
hive.vectorized.execution.enabled=true.

## Bucketing
(SET hive.enforce.bucketing=true;) every time before writing data to the bucketed table. To leverage the bucketing in the join operation we should SET hive.optimize.bucketmapjoin=true. This setting hints to Hive to do bucket level join during the map stage join. It also reduces the scan cycles to find a particular key because bucketing ensures that the key is present in a certain bucket.


## Join algorithms in Hive
Hive only supports equi-Join currently. Hive Join algorithm can be any of the following:

### Multi way Join
If multiple joins share the same driving side join key then all of those joins can be done in a single task.

Example: (R1 PR1.x=R2.a  - R2) PR1.x=R3.b - R3) PR1.x=R4.c - R4

All of the join can be done in the same reducer, since R1 will already be sorted based on join key x.

### Common Join
Use Mappers to do the parallel sort of the tables on the join keys, which are then passed on to reducers. All of the tuples with same key is given to same reducer. A reducer may get tuples for more than one key. Key for tuple will also include table id, thus sorted output from two different tables with same key can be recognized. Reducers will merge the sorted stream to get join output.

### Map Join
SELET /* +MAPJOIN(a,b) */
Useful for star schema joins, this joining algorithm keeps all of the small tables (dimension tables) in memory in all of the mappers and big table (fact table) is streamed over it in the mapper. This avoids shuffling cost that is inherent in Common-Join. For each of the small table (dimension table) a hash table would be 

Map joins are really efficient if a table on the other side of a join is small enough to fit in the memory.
Hive supports a parameter, hive.auto.convert.join, which when it’s set to “true” suggests that Hive try to map join automatically.

## LLAP
<https://cwiki.apache.org/confluence/display/Hive/LLAP>
Also known as Live Long and Process, LLAP provides a hybrid execution model.  It consists of a long-lived daemon which replaces direct interactions with the HDFS DataNode, and a tightly integrated DAG-based framework.
Functionality such as caching, pre-fetching, some query processing and access control are moved into the daemon.  Small/short queries are largely processed by this daemon directly, while any heavy lifting will be performed in standard YARN containers.

## TEZ
Apache Tez generalizes the MapReduce paradigm to execute a complex DAG (directed acyclic graph) of tasks. Refer to the following link for more info.

http://hortonworks.com/blog/apache-tez-a-new-chapter-in-hadoop-data-processing/



## configuration
hive-site.xml
hive.execution.engine = mr tez spark
hive.execution.mode = container llap
hive.exec.max.created.files
hive.exec.max.dynamic.partitions.pernode (default value being 100) is the maximum dynamic partitions that can be created by each mapper or reducer.

hive.exec.max.created.files 

hive.exec.max.dynamic.partitions

hive.merge.mapfiles=true 

hive.merge.mapredfiles=true

hive> set mapred.reduce.tasks=32;

ensure the bucketing flag is set (SET hive.enforce.bucketing=true;) 
every time before we write data to the bucketed table.

SET hive.exce.parallel=true;
complex Hive queries commonly are translated to a number of map reduce jobs that are executed by default sequentially. Often though some of a query’s map reduce stages are not interdependent and could be executed in parallel.

They then can take advantage of spare capacity on a cluster and improve cluster utilization while at the same time reduce the overall query executions time. The configuration in Hive to change this behaviour is a merely switching a single flag SET hive.exce.parallel=true;.

## LEFT SEMI JOIN
In order check the existence of a key in another table, the user can use LEFT SEMI JOIN as illustrated by the following example.
```
INSERT OVERWRITE TABLE pv_users
SELECT u.*
FROM user u LEFT SEMI JOIN page_view pv ON (pv.userid = u.id)
WHERE pv.date = '2008-03-03';
```

##  Dynamic-Partition Insert

This is multi-insert:
```
FROM page_view_stg pvs
INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country='US')
       SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, null, null, pvs.ip WHERE pvs.country = 'US'
INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country='CA')
       SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, null, null, pvs.ip WHERE pvs.country = 'CA'
INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country='UK')
       SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, null, null, pvs.ip WHERE pvs.country = 'UK';
```
Dynamic-partition insert (or multi-partition insert) :
In the dynamic partition insert, the input column values are evaluated to determine which partition this row should be inserted into. If that partition has not been created, it will create that partition automatically. Using this feature you need only one insert statement to create and populate all necessary partitions. 
```
FROM page_view_stg pvs
INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country)
       SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, null, null, pvs.ip, pvs.country
```       

If the input column value is NULL or empty string, the row will be put into a special partition, whose name is controlled by the hive parameter hive.exec.default.partition.name. The default value is HIVE_DEFAULT_PARTITION{}. Basically this partition will contain all "bad" rows whose value are not valid partition names


set hive.exec.dynamic.partition.mode=nonstrict;
```
beeline> FROM page_view_stg pvs
      INSERT OVERWRITE TABLE page_view PARTITION(dt, country)
             SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, null, null, pvs.ip,
                    from_unixtimestamp(pvs.viewTime, 'yyyy-MM-dd') ds, pvs.country
             DISTRIBUTE BY ds, country;
```             
This query will generate a MapReduce job rather than Map-only job. The SELECT-clause will be converted to a plan to the mappers and the output will be distributed to the reducers based on the value of (ds, country) pairs. The INSERT-clause will be converted to the plan in the reducer which writes to the dynamic partitions.


## ARRAYS , explode, inline, stack  LATERAL VIEW

<https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView>
Built-in Table-Generating Functions (UDTF):
* explode() takes in an array (or a map) as an input and outputs the elements of the array (map) as separate rows. \
* posexplode() is similar to explode ; it returns the element as well as its position in the original array.

* inline() explodes an array of structs to multiple rows. Returns a row-set with N columns (N = number of top level elements in the struct), one row per 

* stack() Breaks up n values V1,...,Vn into r rows. Each row will have n/r columns. r must be constant.

Lateral view is used in conjunction with user-defined table generating functions such as explode(). 
```
SELECT pageid, adid
FROM pageAds LATERAL VIEW explode(adid_list) adTable AS adid;

CREATE TABLE array_table (int_array_column ARRAY<INT>);

select explode(array('A','B','C'));
select explode(array('A','B','C')) as col;
select tf.* from (select 0) t lateral view explode(array('A','B','C')) tf;
select tf.* from (select 0) t lateral view explode(array('A','B','C')) tf as col;
select posexplode(array('A','B','C'));


select inline(array(struct('A',10,date '2015-01-01'),struct('B',20,date '2016-02-02')));
```

## Custom Map/Reduce Scripts

 TRANSFORM clause   embeds the mapper and the reducer scripts.
```
SELECT TRANSFORM(pv_users.userid, pv_users.date) USING 'map_script' AS dt, uid CLUSTER BY dt FROM pv_users;
```

## Keywords MAP and REDUCE
MAP and REDUCE are "syntactic sugar" for the more general select transform:

```
FROM (
     FROM pv_users
     MAP pv_users.userid, pv_users.date
     USING 'map_script'
     AS dt, uid
     CLUSTER BY dt) map_output
 
 INSERT OVERWRITE TABLE pv_users_reduced
     REDUCE map_output.dt, map_output.uid
     USING 'reduce_script'
     AS date, count;
 ```    
## Distribute by Clustered by Sort by
<https://cwiki.apache.org/confluence/display/Hive/LanguageManual+SortBy>


the corresponding tables we want to join on have to be set up in the same manner with the joining columns bucketed and the bucket sizes being multiples of each other to work. The second part is the optimized query for which we have to set a flag to hint to Hive that we want to take advantage of the bucketing in the join (SET hive.optimize.bucketmapjoin=true;).
Hive uses the columns in SORT BY to sort the rows before feeding the rows to a reducer.

Difference between Sort By and Order By
Hive supports SORT BY which sorts the data per reducer. The difference between "order by" and "sort by" is that the former guarantees total order in the output while the latter only guarantees ordering of the rows within a reducer. If there are more than one reducer, "sort by" may give partially ordered final results.

Cluster By and Distribute By are used mainly with the Transform/Map-Reduce Scripts. But, it is sometimes useful in SELECT statements if there is a need to partition and sort the output of a query for subsequent queries.

Cluster By is a short-cut for both Distribute By and Sort By.

Hive uses the columns in Distribute By to distribute the rows among reducers. All rows with the same Distribute By columns will go to the same reducer. However, Distribute By does not guarantee clustering or sorting properties on the distributed keys.

## Co-Groups

```
FROM (
     FROM (
             FROM action_video av
             SELECT av.uid AS uid, av.id AS id, av.date AS date
 
            UNION ALL
 
             FROM action_comment ac
             SELECT ac.uid AS uid, ac.id AS id, ac.date AS date
     ) union_actions
     SELECT union_actions.uid, union_actions.id, union_actions.date
     CLUSTER BY union_actions.uid) map
 
 INSERT OVERWRITE TABLE actions_reduced
     SE
```