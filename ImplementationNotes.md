
### Prototype task-list

List of visualizations from Insights we would like to reproduce:


#### Bar Charts

- [x] Average dwell time by day of week (the first of [these](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/dwell?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-summary) charts using bay_events)
- [x] Average dwell time by zone (the second of [these](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/dwell?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-summary) chart using bay_events)
- [x] Average dwell time by bay type (the third of [these](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/dwell?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-summary) chart using bay_events)

#### Time series charts
- [ ] Hourly occupancy across a parking lot for a specified time interval (the first of [these](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/occupancy?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-hourly-occupancy) charts using FLL_Occupancy_Events)
- [ ] Hourly occupancy for a site by day of week for a specified time interval (the second of [these](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/occupancy?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-hourly-occupancy) charts using FLL_Occupancy_Events)
- [ ] Hourly occupancy for a site by zone for a specified time interval (the third of [these](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/occupancy?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-hourly-occupancy) charts using FLL_Occupancy_Events)
- [ ] Hourly occupancy for a site by bay type for a specified time interval (the fourth of [these](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/occupancy?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-hourly-occupancy) charts using FLL_Occupancy_Events)


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

