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

## Populate the CTI card

## View CTI dashboards

## View the CTI row renderer

## Use the CTI timeline template
