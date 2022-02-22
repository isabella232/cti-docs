---
title: "performance"
url: "/performance/"
---
# performance
## Indicator Match Rule Troubleshooting
Possible issues that have been observed before can be grouped in the following categories:
#### Rules are failing due to number of alerts

This usually manifests as a rule failure: `Bulk Indexing of signals failed: [parent] Data too large`, indicating that the alerts payload was simply too large to process. This could be caused by anything upstream of the alert generation: bad/greedy indicator data, a misconfigured rule, or simply too many events/matches. If nothing obvious is misconfigured, try executing the rule against a subset of the original data and continue diagnosis.

#### Indicator match rules are timing out

This occurs as another rule failure: `An error occurred during rule execution: message: "Request Timeout after 90000ms"` and indicates that the query phase is timing out. Similar to the above, inspect the time frame and indices that this rule(s) is querying and try to limit this, or break into multiple rules, etc. See also the General Guidelines below.

#### General Slowness

Due to the amount of in-memory work that indicator match rules perform, having a large number of rules running in parallel can cause noticeable performance implications to kibana.
   
### Reduce the number of indicators
Since the number of indicators directly relates to the amount of work to be performed, filtering out unnecessary indicators is a quick way to improve performance. This can most easily be done with the indicator query (rule parameter: threat_query). If, for example, your indicator indices contain two types of data, but your rule is only concerned with one of them, a threat query of `threat.indicator.dataset: “my_dataset”` will reduce the number of indicators that must be iterated through.
### Reduce the number of source documents
Similar to the above, narrowing the query (rule parameter: query) of your source indices may also improve performance. If, as above, your mapping includes source.port -> threat.indicator.port, defining a query of source_port: * can help ensure that irrelevant documents are not included in the search.

## Benchmarking with kbn-alert-load
[kbn-alert-load](https://github.com/elastic/kbn-alert-load) is a library for load testing the alerting framework. It is also useful for establishing performance benchmarks for the Indicator Match rule. 