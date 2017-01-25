
### Prototype task-list

List of visualizations from Insights we would like to reproduce using either Superset or Pivot on top of Druid:


#### Bar Charts 

- [x] Average dwell time by day of week 
- [x] Average dwell time by zone 
- [x] Average dwell time by bay type 

Insight's versions of these charts are [here](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/dwell?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-summary).

#### Time series charts
- [ ] Hourly occupancy across a parking lot for a specified time interval 
- [ ] Hourly occupancy for a site by day of week for a specified time interval 
- [ ] Hourly occupancy for a site by zone for a specified time interval 
- [ ] Hourly occupancy for a site by bay type for a specified time interval

Insight's versions of these charts are [here](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/occupancy?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-hourly-occupancy).

### Notes
#### Jan 19, 2017
* Avi reviewed the advantages of Druid's data model over our current implementation. 
* Avi explained how Druid ingests data and provided us with a simple dataset and ingestion specs
* Installed Imply (Druid+Plywood+Pivot) and Superset, played with some simple visualizations

After our meeting, we were all convinced we should move ahead with Druid and that a good way to learn it while testing feasibility would be implement some simple visualizations from Insights on top of Druid. We figured we'd start by using Superset and Pivot to ease the learning curve and move on to raw Druid queries to generate plot data for more complicated charts/queries. 

We also found that terminology used in Druid, Pivot and Superset can be redundant, unclear and inconsistent across the three. We should build a glossary to help with this.

Ingesting data in Druid requires ingestion specs. Notes about how to configure these are in Druid_DataModel_Notes.md in this folder. 


#### Jan 20, 2017
* Prototyping dwell time bar charts in Superset
* Understanding Druid queries
* Obtained a day_of_week derived dimension from the data's timestamps (DimensionSpec)

Dimension Spec for day of the week (specified within the JSON of the day_of_week column (derived dimension) we create in Superset)

```
{
  "type" : "extraction",
  "dimension" : "__time",
  "outputName" :  "day_of_week",
  "extractionFn" : {
    "type" : "timeFormat",
    "format" : "EEEE",
    "timeZone" : "America/New York",
    "locale" : "en"
  }
}
```

#### Jan 23, 2017
* postAggregations in Superset
* Prototyping the remainder of the dwell time bar charts.

#### Jan 25, 2017
* Prototyping time series charts in Superset
* Processing raw time series chart data using Druid API
* Defining a time series query to obtain fractional occupancy by hour of day (this is a work in progress)
