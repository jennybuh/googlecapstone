## Cyclistic - Bike Rental Data Analysis 

##Consolidated quarterly data on BigQuery, writing columns to a new table with full annual data. Q1 and Q2 were also combined.

```sql
SELECT  
    trip_id,
    start_time,
    end_time,
    bikeid,
    tripduration,
    from_station_id,
    from_station_name,
    to_station_id,
    to_station_name,
    usertype,
    gender,
    birthyear
FROM `sqltest-323923.divvy_trips.2019_Q3`
UNION ALL
SELECT  
    trip_id,
    start_time,
    end_time,
    bikeid,
    tripduration,
    from_station_id,
    from_station_name,
    to_station_id,
    to_station_name,
    usertype,
    gender,
    birthyear
FROM `sqltest-323923.divvy_trips.2019_Q4`  
```



##Summary statistics for average trip duration and number trips 

```sql
SELECT
    usertype,
    generation,
    gender,
    CONCAT(from_station_name, 'to', to_station_name) AS route,
    COUNT(*) AS num_trips,
    ROUND(AVG(cast(tripduration as int64)),2) AS duration
FROM 
    `sqltest-323923.divvy_trips.2019_annual`

GROUP BY 
    from_station_name, to_station_name, usertype, generation, gender

ORDER BY
    num_trips DESC;
```



##Codifying age ranges and generations, ran this query for all American generations

```sql
UPDATE
    `sqltest-323923.divvy_trips.2019_annual`
SET 
    generation = "G.I."
WHERE 
    birthyear >= 1900 AND birthyear <= 1924;
```

##Created routes by combining start and end stations to preview most and least popular routes

```
SELECT
    usertype, 
    CONCAT(from_station_name, 'to', to_station_name) AS route, 
    COUNT (*) AS num_trips 

FROM
    `sqltest-323923.divvy_trips.2019_Q1_copy`

WHERE 
    usertype = "Subscriber"

GROUP BY 
    from_station_name, to_station_name, usertype; 
```



##Converted start_time and end_time to weekday names

```sql
SELECT
    usertype,
    gender,
    generation,
    start_time,
    end_time,
    tripduration,
    EXTRACT(DAYOFWEEK from start_time) AS starting_day,
    EXTRACT(DAYOFWEEK from end_time) AS ending_day,

FROM 
    `sqltest-323923.divvy_trips.2019_annual
```



##Converted DAYOFWEEK numbers to corresponding weekday to create a summary of weekday trips by all metrics (gender, generation)

```sql
SELECT
     usertype,
     starting_weekday,
     gender,
     generation,
     COUNT(starting_weekday) AS num_days
FROM
     `sqltest-323923.divvy_trips.2019_annual_week`
WHERE
     starting_weekday = "Sunday" OR 
     starting_weekday = "Monday" OR 
     starting_weekday = "Tuesday" OR 
     starting_weekday = "Wednesday" OR 
     starting_weekday = "Thursday" OR 
     starting_weekday = "Friday" OR 
     starting_weekday = "Saturday"
GROUP BY 
    usertype, gender, generation, starting_weekday 
ORDER BY 
    num_days DESC

```