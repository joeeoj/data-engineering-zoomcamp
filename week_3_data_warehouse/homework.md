## Homework
[Form](https://forms.gle/ytzVYUh2RptgkvF79)  
We will use all the knowledge learned in this week. Please answer your questions via form above.  
**Deadline** for the homework is 14th Feb 2022 17:00 CET.

### Question 1: 
**What is count for fhv vehicles data for year 2019**  
Can load the data for cloud storage and run a count(*)

```sql
-- 321.08 MB processed
SELECT COUNT(*) 
FROM `dtc-de-course-353020.trips_data_all.fhv_tripdata`
WHERE
  EXTRACT(year FROM pickup_datetime) = 2019
```

Answer: 42,084,899

### Question 2: 
**How many distinct dispatching_base_num we have in fhv for 2019**  
Can run a distinct query on the table from question 1

```sql
-- How many distinct dispatching_base_num do we have in fhv for 2019
-- 642.97MB processed
SELECT
  COUNT(DISTINCT dispatching_base_num) as distinct_dispatching_base_nums
FROM `dtc-de-course-353020.trips_data_all.fhv_tripdata`
WHERE
  EXTRACT(year FROM pickup_datetime) = 2019
```

Answer: 792

### Question 3: 
**Best strategy to optimise if query always filter by dropoff_datetime and order by dispatching_base_num**  
Review partitioning and clustering video.   
We need to think what will be the most optimal strategy to improve query 
performance and reduce cost.

Answer: Partition by dropoff_datetime and cluster by dispatching_base_num

### Question 4: 
**What is the count, estimated and actual data processed for query which counts trip between 2019/01/01 and 2019/03/31 for dispatching_base_num B00987, B02060, B02279**  
Create a table with optimized clustering and partitioning, and run a 
count(*). Estimated data processed can be found in top right corner and
actual data processed can be found after the query is executed.

```sql
-- create table
CREATE OR REPLACE TABLE `dtc-de-course-353020.trips_data_all.fhv_partitioned_clustered`
PARTITION BY DATE(dropoff_datetime)
CLUSTER BY dispatching_base_num
AS
SELECT * FROM `trips_data_all.fhv_tripdata`
;
```

```sql
-- query data
-- What is the count, estimated and actual data processed for query which counts trip
-- between 2019/01/01 and 2019/03/31 for dispatching_base_num B00987, B02060, B02279
SELECT COUNT(*)
FROM `dtc-de-course-353020.trips_data_all.fhv_partitioned_clustered`
WHERE
  (dropoff_datetime BETWEEN '2019-01-01' AND '2019-03-31')
  AND dispatching_base_num IN ('B00987', 'B02060', 'B02279')
;
```

* Estimated: 400.07 MB
* Actual: 164.25 MB 
* Count: 26,558

### Question 5: 
**What will be the best partitioning or clustering strategy when filtering on dispatching_base_num and SR_Flag**  
Review partitioning and clustering video. 
Partitioning cannot be created on all data types.

Partition on dispatching_base_num and cluster on SR_Flag. SR_Flag can't be used for the partition because it is a BOOL.

### Question 6: 
**What improvements can be seen by partitioning and clustering for data size less than 1 GB**  
Partitioning and clustering also creates extra metadata.  
Before query execution this metadata needs to be processed.

Answer: It doesn't typically help performance for datasets < 1 GB because of the metadata overhead. Ideally each partition is > 1 GB each.

### (Not required) Question 7: 
**In which format does BigQuery save data**  
Review big query internals video.

Answer: In a columnar format.
