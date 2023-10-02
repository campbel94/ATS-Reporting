# ATS-Reporting
Repo for all ATS reporting, forecasting and data viz

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