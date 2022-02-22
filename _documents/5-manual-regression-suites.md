---
title: "manual regression suites"
url: "/manual-regression-suites/"
---
# manual regression suites

## Create an alert from an indicator match rule
- run Elasticsearch + kibana locally, or deploy a cloud instance
- navigate to Kibana Dev Tools
- create an event index with the following query

```
PUT test-index
```

- add the date mapping for the `@timestamp` field

```
PUT test-index/_mapping
{
  "properties": {
    "@timestamp": {
      "type": "date"
    }
  }
}
```
- get a fresh timestamp value by opening the browser javascript console and executing 

```
new Date().valueOf()
```

- copy the value (for example, `1645547413852`) and make the following request to create a source event. 
Note: in this example, a field named `test-field` is added to the event. Indicator Match rules work with all events, but ECS compliant fields are recommended for use.

```
POST test-index/_doc
{
  "@timestamp": 1645547413852,
  "test-field": "test-value"
}
```

- create a threat index with a date type `@timestamp` field.

```
PUT threat-index

PUT threat-index/_mapping
{
  "properties": {
    "@timestamp": {
      "type": "date"
    }
  }
}

PUT threat-index/_doc
{
  "@timestamp": 1645547413852,
  "threat-field": "test-value"
}
```

- Create a new indicator match rule, with `test-index` as the index value, `threat-index` as the threat index value.
- Add `test-field` to index field, and `threat-field` to indicator field to create the threat mapping.
- Create the rule with a name, description, schedule, and interval. 
- Alert will appear on the alerts table once the rule execution is complete.
- For better indicator documents, [setup a threat intel integration](#setting-up-fleet-threat-integrations) and use a field from there to match an event.
  


## Create an investigation time enrichment
- run Elasticsearch + kibana locally, or deploy a cloud instance
- navigate to Kibana Dev Tools
- create an event index with the following query

```
PUT test-index
```

- add the date mapping for the `@timestamp` field

```
PUT test-index/_mapping
{
  "properties": {
    "@timestamp": {
      "type": "date"
    }
  }
}
```
- get a fresh timestamp value by opening the browser javascript console and executing

```
new Date().valueOf()
```

- copy the value (for example, `1645547413852`) and make the following request to create a source event. Make sure to include a field that is suitable for [investigation time enrichments]((#invetigation-time-enrichments) ).
  Note: in this example, `file.hash.md5` is used for creating the investigation time enrichment. Also, a field named `test-field` is added to the event. Indicator Match rules work with all events, but ECS compliant fields are recommended for use.
  

```
POST test-index/_doc
{
  "@timestamp": 1645547413852,
  "test-field": "test-value",
  "file": {
    "hash: {
      "md5": "test-md5"
    }
  }
}
```


- create a threat index with a date type `@timestamp` field, and add the value requied for the investigation time enrichment (in this example, `threat.indicator.file.hash.md5`)

```
PUT threat-index

PUT threat-index/_mapping
{
  "properties": {
    "@timestamp": {
      "type": "date"
    }
  }
}

PUT threat-index/_doc
{
  "@timestamp": 1645547413852,
  "threat": {
    "indicator": {
      "file": {
        "hash": {
          "md5": "test-md5"
        }
      }
    }
  }
}
```

- add `threat-index` to [defaultThreatIndex](#cti-contants) UI setting in Stack Management / Advanced Settings
- create any rule that would pick up on the source event, such as a custom query rule with the index `test-index` and query `test-field:*`
- once the alert is created, expand the alert to view it on the [Alert Flyout](#alert-details-flyout). You will find the investigation time enrichment on the `file.hash.md5` field on the Overview tab and the Threat Intel tab. 

## Populate the CTI card

- [Enable one of the threat intel integrations](#setting-up-fleet-threat-integrations) to view the CTI card populated on the Security Overview page.

## View CTI dashboards
- [Enable one of the threat intel integrations](#setting-up-fleet-threat-integrations) to obtain the dashboards.
- Click on the `Source` link on the [CTI Card](#threat-intelligence-card) for the dashboard you would like to view
- You can also visit Analyze / Dashboards and search for the `threat intel` tag to find any threat intel dashboards.


## View the CTI row renderer
- Create an alert with threat intelligence enrichments by using the indicator match rule ([instructions](#create-an-alert-from-an-indicator-match-rule))
- Find the alert on the alert summary table and click on the timeline view button.
- You can now see the alert automatically using the [CTI row renderer](#cti-row-renderer) view.

## Use the CTI timeline template
- Navigate to Security / Timelines / Templates and click on `Generic Threat Match Template` to view and use the threat match template.