
### Prototype task-list

List of visualizations from Insights we would like to reproduce:

- [ ] Average dwell time by day of week (replicate [this](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/dwell?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-summary) chart using bay_events)
- [ ] Average dwell time by zone (replicate this chart using bay_events)
- [ ] 


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

