# ATS-Reporting
Repo for all ATS reporting, forecasting and data viz

## BigQuery Tables
### liveramp-eng-pie.ats_metrics.ats_requests_by_publisher|
|Field name |  Type  |
|-----------|--------|
| day       |   DATE |
|-----------|--------|
| country_code |   STRING |
|-----------|--------|
| publisher |   INTEGER |
|-----------|--------|
| config_name |   STRING |
|-----------|--------|
|request_types| STRING |
|-----------|--------|
| total_request | FLOAT |
- day
- country_code
- publisher
- config_name
- request_types	
- total_request

liveramp-eng-pie.ats_metrics.ats_requests_by_publisher_AU
- day
- country_code
- publisher_id
- config_name
- request_types	
- total_request

liveramp-eng-ps.ats_reporting.ats_publisher_stats
- publisher_id
- domain
- browser_type
- os_type
- device_type
- city
- country_code
- envelope_request_type
- request_date
- rampid_sketch	
- rampid_source
- request_count

## SQL Queries
Non AU Countries:
```SQL
SELECT day, country_code, publisher, config_name, request_types, SUM(total_request) as requests
FROM `liveramp-eng-pie.ats_metrics.ats_requests_by_publisher`
WHERE day >= {{START_DATE}}
AND day <= {{END_DATE}}
AND country_code IN('AR','AU','BE','BR','CA','FR','DE','GB','HK','ID','IT','JP','MX','NL','NZ','PL','RO','SG','ES','TW','US')
GROUP BY day, country_code, publisher, config_name, request_types
ORDER BY day, country_code, publisher, request_types
```
AU:
```SQL
SELECT day, country_code, publisher_id, config_name, request_types, SUM(total_request) as requests
FROM `liveramp-eng-pie.ats_metrics.ats_requests_by_publisher_AU`
WHERE day >= '2023-09-01'
AND day <= '2023-09-30'
GROUP BY day, country_code, publisher_id, config_name, request_types
ORDER BY day, country_code, publisher_id, request_types
```