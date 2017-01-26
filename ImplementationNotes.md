
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

### Daily notes
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

Derived dimensions in Superset can be also specified as "Druid Metrics". Metrics in Supersetese are just numeric columns/metrics but are either aggregations (like sum, max etc.) or postAggregations. We created an "average visit duration" metric defined as below:

```
{ "type": "arithmetic",
      "name": "avg_visit_duration",
      "fn": "/",
      "fields": [
        { "type": "fieldAccess", "fieldName": "sum__dwell_mins" },
        { "type": "fieldAccess", "fieldName": "count" }
      ]
    }
  ```
  
 Here, ```sum__dwell_mins``` and ```count``` are aggregations defined on the ingested dataset. 

#### Jan 25, 2017
* Prototyping time series charts in Superset
* Processing raw time series chart data using Druid API
* Defining a time series query to obtain fractional occupancy by hour of day (this is a work in progress)

Having worked on a few bar charts, we decided to move on to the visualization of time series. For this, we obtained a dataset that reports the bays used and present, named ```bays_occupied_qty``` and ```bays_supplied_qty``` respectively, at every half hour interval, for every zone name and bay type, for the Fort Lauderdale site between Aug 20 and Sept 21, 2016. 

For each row (reported every half-hour, for every zone and bay type), the fraction of bays that are occupied is the ratio of bays occupied to the total bays. We cast this ratio, which we called ```fraction_occupied``` as a postAggregation defined on aggregations derived by rolling up ```bays_occupied_qty``` and ```bays_supplied_qty``` across any desired set of groupings:

```
    { "type": "arithmetic",
      "name": "fractional_occupancy",
      "fn": "/",
      "fields": [
        { "type": "fieldAccess", "fieldName": "sum__bays_occupied_qty" },
        { "type": "fieldAccess", "fieldName": "sum__bays_supplied_qty" }
      ]
    }
 ```   
 
Visualizing fraction_occupied over time allowed us to visualize the opccupancy as a function of time between a user specified set of start and end times. 
 
Next, we attempted averaging along the time axis, so as to enable visualizations of the average value of a metric over the hours of a day like [here](https://insights.parkassist.com/en/sites/ft-lauderdale/reports/occupancy?end_date=20160921&end_time=23%3A30&start_date=20160820&start_time=00%3A00#tab-hourly-occupancy) or the days of a week like [here] (https://insights.parkassist.com/en/sites/ft-lauderdale/chart_builder?utf8=%E2%9C%93&aggregation=avg&y_axis=dwell&x_axis=day-of-week&chart_type=line&range%5B1%5D%5Bname%5D=Series+1&range%5B1%5D%5Bstart_date%5D=2016-08-20&range%5B1%5D%5Bend_date%5D=2017-09-20&range%5B1%5D%5Bstart_time%5D=00%3A00&range%5B1%5D%5Bend_time%5D=22%3A00&range%5B1%5D%5Bdays_of_week%5D%5B%5D=8&range%5B1%5D%5Bzones%5D%5B%5D=All+zones&commit=Update)

Doing this in Superset was complicated by its lacking features that allow us to define postAggregations that are functions of other postAggregations. This is because postAggregastions in Superset are metrics that do not become columns and which cannot be rolled up. So for example, we could not create the following postAggregation:

```
{ "type": "arithmetic",
      "name": "average_occupancy",
      "fn": "/",
      "fields": [
        { "type": "fieldAccess", "fieldName": "sum__fraction_occupied" },
        { "type": "fieldAccess", "fieldName": "count" }
      ]
    }
 ```
 
 To deal with this, we decided to separate the task of data processing from visualization and attempted to write a Druid timeSeries query that would give us average occupancy (expressed as a fraction or percentage) averaged over the hour of the day. We constructed:
 
 ```
 {
  "aggregations": [
    {
      "type": "longSum",
      "name": "sum__bays_occupied_qty",
      "fieldName": "bays_occupied_qty"
    },
    {
      "type": "longSum",
      "name": "sum__bays_supplied_qty",
      "fieldName": "bays_supplied_qty"
    },
    {
      "type": "count",
      "name": "count"
    }
  ],
  "intervals": "2016-09-04T00:00:00+00:00/2016-09-11T00:00:00+00:00",
  "dataSource": "FLL_Occupancy_Events",
  "granularity": {
    "duration": 1800000,
    "timezone": "America/New_York",
    "type": "duration"
  },
  "postAggregations": [
    {
      "type": "javascript",
      "name": "average_occupancy",
      "fieldNames": ["sum__bays_occupied_qty", "sum__bays_supplied_qty"],
      "function": "function(sum__bays_occupied_qty, sum__bays_supplied_qty, count) { return sum__bays_occupied_qty / sum__bays_supplied_qty; }"
    }
  ],
  "queryType": "timeseries"
}
```

This query produced an output that we need to visualize and validate. We also need to figure out why it reports a count of 23. 

 
 
