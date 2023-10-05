# ATS-Reporting
Repo for all ATS reporting, forecasting and data viz

## BigQuery Tables
### liveramp-eng-pie.ats_metrics.ats_requests_by_publisher
|Field name     |  Type   |
|---------------|---------|
| day           |   DATE  |
| country_code  | STRING  |
| publisher     | INTEGER |
| config_name   |  STRING |
|request_types  |  STRING |
| total_request |   FLOAT |

### liveramp-eng-pie.ats_metrics.ats_requests_by_publisher_AU
|Field name     |  Type   |
|---------------|---------|
| day           | DATE    |
| country_code  | STRING  |
| publisher_id  | INTEGER |
| config_name   | STRING  |
| request_types | STRING  |
| total_request | FLOAT   |

### liveramp-eng-ps.ats_reporting.ats_publisher_stats
|Field name             |  Type   |
|-----------------------|---------|
| publisher_id          | INTEGER |
| domain                | STRING  |
| browser_type          | STRING  |
| os_type               | STRING  |
| device_type           | STRING  |
| city                  | STRING  |
| country_code          | STRING  |
| envelope_request_type | STRING  |
| request_date          | DATE    |
| rampid_sketch         | BYTES   |
| rampid_source         | STRING  |
| request_count         | INTEGER |

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
7 nad 30-day rolling averages
```SQL
SELECT publisher_id, envelope_request_type, request_date, SUM(request_count) AS envelopes, AVG(SUM(request_count))
  OVER (
    ORDER BY request_date
    ROWS BETWEEN 7 PRECEDING AND 0 FOLLOWING
  ) as avg_7_day, AVG(SUM(request_count))
  OVER (
    ORDER BY request_date
    ROWS BETWEEN 30 PRECEDING AND 0 FOLLOWING
  ) as avg_30_day
FROM `liveramp-eng-ps.ats_reporting.ats_publisher_stats` as stats
WHERE publisher_id = 13735
AND request_date >= "2023-07-01"
AND rampid_source = 'not cookie'
-- how to filter out pubs who haven't been live for past two months
GROUP BY publisher_id, country_code, envelope_request_type, request_date
ORDER BY request_date;
```

Pubs Dips to look into:
- McClatchy drop starting 9/27
- Trib & MNG increase on 9/26
- LA Times 9/28