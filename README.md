# DruidExploration

Documentation of early experiments with Druid, a data store optimized for the storage of large time-series datasets.

## Why Druid?

## Installing Druid
### Install stand-alone
### (EASY) Install Imply Analytics Platform:

Follow Avi's installation instructions [here](https://github.com/parkassist/sdp-batch/tree/druid-evaluation/druid-evaluation/quickstart). 
  
## Ingesting data
### JSON specification of ingestion parameters corresponding to a csv or tsv dataset

## Querying ingested data
#### The terminology of Druid's data model



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
