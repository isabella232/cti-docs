---
title: "frontend"
url: "/frontend/"
---
# Frontend
## Indicator Match Rule
### Create Rule UI (Define Step)
<img style="max-width:1200px;" src="/public/images/create_rule_ui.png"/>

1. `index` <-> Source events are queried from the indices specified with this parameter.
2. `query` <-> Source events are queried with the query specified with this parameter.
3. `filters` <-> Source events are filtered with the filters specified with this parameter.
4. `threat_index` <-> Indicator events are queried from the indices specified with this parameter.
5. `threat_query` <-> Indicator events are queried with the query specified with this parameter.
6. `threat_mapping` <-> Source events and Indicator events are matched on the values specified with this parameter. Multiple mappings can be added with AND and OR buttons.

### Create Rule UI (About Step)
<img style="max-width:1200px;" src="/public/images/create_rule_ui_about.png"/>
In the advanced settings section, the value of the `threat_indicator_path` parameter can be set on the input titled "Indicator prefix override". The default value `threat.indicator` is ECS 1.11 compliant.

### Create Rule UI (Schedule Step)
<img style="max-width:1200px;" src="/public/images/create_rule_ui_schedule.png"/>
The default value for the `schedule` parameter is 1h for indicator match rules, and it is recommended to customers not to make the rule execution more frequent to avoid overloading kibana memory.

### Rule Details UI
<img style="max-width:1200px;" src="/public/images/rule_details_ui.png"/>
You can find information about the rule parameters in the rule details UI.

## Alert Details Flyout
### Overview tab
<img style="max-width:1200px;" src="/public/images/alert_details.png"/>
 - section titled "Threat Match Detected" displays the source event field and its value where an [indicator match rule](#indicator-match-rule) has added an enrichment onto an alert.
 - section titled "Enriched with Threat Intelligence" displays the source event field and its value where an [investigation time enrichment](#investigation-time-enrichments) has been added.

### Threat Intel tab
Threat Intel tab shows each threat intelligence enrichment associated with the alert. 

Enrichments added by the indicator match rule are under the "Threat Match Detected" section. Each enrichment can be collapsed / expanded by clicking on the enrichment title.
<img style="max-width:1200px;" src="/public/images/threat_intel_tab_1.png"/>

Enrichments added by Investigation Time enrichment logic can be found under the calendar view. The calendar defaults to a 30 day lookup, which can be modified to change the indicator lookup date.
<img style="max-width:1200px;" src="/public/images/threat_intel_tab_2.png"/>

## Threat Intelligence Integrations
Threat intelligence integrations offered by Fleet can be found on the Integrations page in Kibana. Searching "threat" keyword will yield available threat intel integrations. Fleet Agent must be enabled for ensuring that the indicator events can be ingested. The integrations will populate indices starting with the prefix `logs_ti-*`, which is the default  `defaultThreatIndex` UI setting value from v8.0 onwards. 
<img style="max-width:1200px;" src="/public/images/threat_intel_integrations.png"/>

## Threat Intelligence Card
The Threat Intelligence Card is on the Overview page, and it gives information about the status of the added threat integrations in terms of the indicator events that have been ingested by each integration over the course of the time that has been specified in the top level calendar of the security application.
By default, the Fleet integration API is queried to see if all threat intel integrations have been added via fleet, and if not, a notification is shown to customers to facilitate discovery. The button on the notification takes the users to the Integrations page where the search query "threat" has been applied on the page.
[defaultThreatIndex UI Setting](#cti-constants) is queried to find the indicator counts, as it can be inspected with the "Inspect Query" button on the CTI card. [threat.feed.name](#ecs-threat-fields) value is used to display the feed name on the UI. For non-ECS compliant data, "Other" is used for a combined indicator count.

<img style="max-width:600px;" src="/public/images/cti_card.png"/>

## CTI dashboards
Threat intelligence dashboards links can be found on the Threat Intelligence Card, in the column titled "Source". 

It is also possible to search for threat intelligence dashboards by searching for the "threat intel" tag on the Dashbaords page under the Analyze section of Kibana.
<img style="max-width:1200px;" src="/public/images/dashboard_list.png"/>

A sample dashboard will have information regarding the status of the indicator index.
<img style="max-width:1200px;" src="/public/images/dashboard.png"/>

## CTI Row Renderer

CTI row renderer displays relevant threat intelligence information pertaining to an alert when it is viewed on the timeline view. The purpose of the row renderer is to facilitate threat hunting by having the matching threat field and value as well as the threat feed name in a summary view.

<img style="max-width:1200px;" src="/public/images/row_renderer.png"/>

## CTI Timeline template
The generic threat match template allows customers to review alerts with threat intelligence data in a simple way for threat hunting purposes.

<img style="max-width:1200px;" src="/public/images/timeline_template.png"/>


## CTI Alerts Table filter
Alerts table can be filtered to display events that contain threat intelligence information by checking the following checkbox on the table UI. Currently this setting only applies to alerts that have persistent threat intelligence information (alerts that have been created by Indicator Match rules). Investigation time enrichments can not be filtered into this view as the search for those is triggered only in Alert Summary Flyout view only.
<img style="max-width:1200px;" src="/public/images/table-filter.png"/>