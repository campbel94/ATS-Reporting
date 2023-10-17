# ATS-Reporting
Repo for volume change monitoring (separate from any timeseries / ARIMA models)


## What we want:
Flag the day-over-day, week-over-week and month-over-month changes in envelope volumes (by publisher and possibly country) that are worth investigating.

Example dips that would be worth looking into:
- McClatchy drop starting 9/27
- Trib & MNG increase on 9/26
- LA Times 9/28
- Future Publishing 10/5


## Expect Output
Script that can be run for easy outputs (possibly visualizations)

## SQL Query:
```SQL
SELECT day, country_code, publisher, config_name, request_types, SUM(total_request) as requests
FROM `liveramp-eng-pie.ats_metrics.ats_requests_by_publisher`
WHERE day <= DATE_SUB(CURRENT_DATE, INTERVAL 1 DAY)
AND country_code = 'US'
AND request_types = 'New Envelope'
GROUP BY day, country_code, publisher, config_name, request_types
ORDER BY day desc, country_code, publisher, request_types
```
<b>NOTE:</b> this data is limited to the US and only 'New Envelopes' for the sake of downloading to a local CSV file. Ideally (through Google Collab) this wouldn't be a limitation.

## Steps:
1. Filter the last two months worth of data for just pubs with significanat volume contribution (TBD on what exactly that threshold is).
2. Calculate rolling 7-day and 30-day averages, as well as the perfcentae change (unless that is already accounted for in the SQL query)
3. Filter results for just those above "x" percenage.
4. Display / visualize the results