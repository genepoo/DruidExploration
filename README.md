# DruidExploration

Documentation of early experiments with Druid, a data store optimized for the storage of large time-series datasets.

## Why Druid?

## Installing Druid
### Install stand-alone
### (EASY) Install Imply Analytics Platform:

Follow Avi's installation instructions in the druid-evaluation branch of the [sdp-batch repo](https://github.com/parkassist/sdp-batch/tree/druid-evaluation/druid-evaluation/quickstart). 

## The terminology of Druid's data model
 
1. **Variables** are of three types, stored as **columns**
  1. Time, which is a special column type that is rolled up (when deriving meaningful aggregate measures across the data) or sharded (segmented) to look at what happens within specific time intervals. Segmentation from the point of view of a query is different from segmentation that Druid performs internally at the time of ingestion. The latter depends on the floor granularity specified, among other things, whereas the former is specified at query time, depending on the output desired.
  2. Dimensional columns - strings, categorical variables etc - things you might want to filter and group on but that are not computed on like numbers. 
  3. Metric columns - numeric columns that we might want to compute statistics with.
A more nuanced description of the differences between dimension and metric types is [here.](https://groups.google.com/forum/#!msg/druid-user/Mk6omlC6Vbk/jtIFGFrACwAJ)

2. Druid operates natively on its variables using **JSON over HTTP**, although the community has contributed query libraries in numerous languages, including SQL.
3. Two principal actions in Druid, involved both in the process of compression and when carrying out queries are 
  1. **rolling up:** aggregating the data based on your specifications. e.g. the original data may contain rows measured at millisecond precision, but we want averages taken over every half hour, and
  2. **sharding:** segmenting the data by time intervals. e.g. we have a weeks worth of data but we want to ask questions about a specific day. All queries operate only on sharded/segmented data and therefore are required to specify a time interval upon which they operate, (even if that is "all"?). 
  
## Ingesting or Indexing data
There are several ways to ingest data, but we do it by providing (a) a dataset in csv format and (b) the specifications for how to index and compress these data in an "index-task" JSON file. Avi has uploaded example csv and index-task files in the [repo](https://github.com/parkassist/sdp-batch/tree/druid-evaluation/druid-evaluation/quickstart). 


Druid translates the specs in the index-task file and uses them to parse the data provided in a csv file by:
1. assigning names and types to the coulmns 
2. storing each column separately
3. compressing metric columns using efficient LZ4 compression 
4. compressing dimensional columns using a combination of:
  1. a dictionary that assigns each unique value in the column a numeric id (e.g. Monday: 1, Tuesday:2, etc)
  2. column data specified using the number from (a) e.g. the column {"Monday", "Monday", "Tuesday"} becomes {1, 1, 2}
  3. a bitmap with as many rows as there are unique values in the column. 

For datasets where the number of possible values is low but the total number of rows is high (e.g. the day of week column in a dataset sampled every minute for a month), the bitmaps will be very sparse and hence easy to compress. 
  
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
2. 
