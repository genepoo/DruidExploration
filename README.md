# DruidExploration

Documentation of early experiments with Druid, a data store optimized for the storage of large time-series datasets.

## Why Druid?

## Installing Druid
### Install stand-alone
### (EASY) Install Imply Analytics Platform:

Follow Avi's installation instructions in the druid-evaluation branch of the [sdp-batch repo](https://github.com/parkassist/sdp-batch/tree/druid-evaluation/druid-evaluation/quickstart). 
  
## Ingesting or Indexing data
There are likely other ways to ingest data, but we do it by providing (a) a dataset in csv format and (b) the specifications for how to index and compress these data in an "index-task" JSON file. Avi also uploaded example csv and index-task files in the [repo](https://github.com/parkassist/sdp-batch/tree/druid-evaluation/druid-evaluation/quickstart). 


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
#### The terminology of Druid's data model
1. There are three types of columns
  1. Time, which is a special column type that is **rolled up** (when deriving meaningful aggregate measures across the data) or **sharded (segmented)** to look at what happens within specific time intervals. Segmentation from the point of view of a query is different from segmentation that Druid performs internally at the time of ingestion. The latter depends on the floor granularity specified, among other things, whereas the former is specified at query time, depending on the output desired. 


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
