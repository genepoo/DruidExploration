# Druid's data model

## The terminology of Druid
 
1. **Variables** are of three types, stored as **columns**
  1. Time, which is a special column type that is rolled up (when deriving meaningful aggregate measures across the data) or sharded (segmented) to look at what happens within specific time intervals. Segmentation from the point of view of a query is different from segmentation that Druid performs internally at the time of ingestion. The latter depends on the floor granularity specified, among other things, whereas the former is specified at query time, depending on the output desired.
  2. Dimensional columns - strings, categorical variables etc - things you might want to filter and group on but that are not computed on like numbers. 
  3. Metric columns - numeric columns that we might want to compute statistics with.
A more nuanced description of the differences between dimension and metric types is [here.](https://groups.google.com/forum/#!msg/druid-user/Mk6omlC6Vbk/jtIFGFrACwAJ)

2. Druid operates natively on its variables using **JSON over HTTP**, although the community has contributed query libraries in numerous languages, including SQL.

3. Two principal actions in Druid, involved both in the process of compression and when carrying out queries are 

  1. **Rolling up:** aggregating the data based on your specifications. e.g. the original data may contain rows measured at second precision, but we want averages taken over every half hour, and
  2. **Sharding:** segmenting the data by time intervals. e.g. we have a weeks worth of data but we want to ask questions about a specific day. All queries operate only on sharded/segmented data and therefore are required to specify a time interval upon which they operate, (even if that is "all"?). 
  
4. **Granularity** has to do with the units of time relevant to an operation. It is used in two contexts.

 1. Ingestion Granularity: Granularity parameters in the ingestion spec tell Druid how data should segmented+compressed at ingestion. Two things are relavant at the time of ingestion - the floor of the desired granularity, which is the smallest unit of time that one might want to query the data on, and influences how the data are rolled up. This is specified using the **queryGranularity** field in the ingestion granularity spec. The other is **segmentGranularity**, which is the time period covered by each shard or segment during ingestion. 
 2. Query Granularity: Some queries/operation must specify a granularity across which output is desired. We go into examples when we go over queries.
 
  
## Ingesting or Indexing data
There are several ways to ingest data, but we do it by providing (a) a dataset in csv format and (b) the specifications for how to index and compress these data in an "index-task" JSON file. Avi has uploaded example csv and index-task files in the druid-evaluation branch of the sdp-batch [repo](https://github.com/parkassist/sdp-batch/tree/druid-evaluation/druid-evaluation/quickstart). 


Druid translates the specs in the index-task file and uses them to parse the data provided in a csv file by (a) assigning names and types to the coulmns, (b) storing each column separately and (c) compressing each column. Metric columns are compressed using [LZ4 compression](https://en.wikipedia.org/wiki/LZ4_(compression_algorithm)). Dimensional columns are compressed using a combination of:
  1. a dictionary that assigns each unique value in the column a numeric id (e.g. Monday: 1, Tuesday:2, etc)
  2. column data specified using the number from (a) e.g. the column {"Monday", "Monday", "Tuesday"} becomes {1, 1, 2}
  3. a bitmap with as many rows as there are unique values in the column. 

For datasets where the number of possible values is low but the total number of rows is high the bitmaps will be very sparse, leading to large space savings with compression.
  
### JSON specification of ingestion parameters corresponding to a csv or tsv dataset

## Querying ingested data

#### Types of queries: Timeseries, TopN and groupBy
#### Transforming data: Aggregations and postAggregations
#### Druid API queries
#### Plywood queries (used to be Facet.js)
#### Pydruid 
  
## Visualizing query results
### Aggregations and postAggregations in Superset
### Pivot, meet Plywood.
### Pydruid, the Gandalf of them all. 


### Notes

When specifying ingestion spec, remember that in 
```
      "dataSource" : “trisite_bay_events",
      "granularitySpec" : {
        "type" : "uniform",
        "segmentGranularity" : "day",
        "queryGranularity" : "minute",
        "intervals" : ["2016-11-01/2016-11-30”]
 ```       
**"type" = "uniform"** means that we want the segments to be sharded uniformly. It doesn't mean that the timestamps on the rows of the input are uniform. i.e. - this specifies **desired spec of the segmented output**, not the input. 


