# Athena Exercise
An Athena exercise with partitioned data

1. Load the files in the year 2023 into a dedicated bucket in S3
2. Open Athena
3. On the Athena console, choose 'Query editor'.
4. Create a database with the following command:
```sql
CREATE DATABASE US_IMMIGRANTS;
```
5. In the Data pane, for Database, choose the database `us_immigrants`.
6. Create sample table from S3 using JSON SerDe:
```sql
CREATE EXTERNAL TABLE impressions (
    requestbegintime string,
    adid string,
    impressionid string,
    referrer string,
    useragent string,
    usercookie string,
    ip string,
    number string,
    processid string,
    browsercookie string,
    requestendtime string,
    timers struct
                <
                 modellookup:string, 
                 requesttime:string
                >,
    threadid string, 
    hostname string,
    sessionid string
)   
PARTITIONED BY (dt string)
ROW FORMAT SERDE 'org.apache.hive.hcatalog.data.JsonSerDe'
LOCATION 's3://us-east-1.elasticmapreduce/samples/hive-ads/tables/impressions';
```
7. Then load all partitions:
```sql
MSCK REPAIR TABLE impressions;
```
8. Now print a preview of the table by clicking the three dots near the table name.
9. Create your own data table:
```sql
CREATE EXTERNAL TABLE immigrants (
    Port_Name string,
    State string,
    Port_Code int,
    Border string,
    Date string,
    Measure string,
    Value int,
    Latitude float,
    Longitude float,
    Point string
)   
PARTITIONED BY (year string, month string)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    "separatorChar" = ",",
    "quoteChar" = '"',
    "skip.header.line.count" = "1"
)
STORED AS TEXTFILE
LOCATION 's3://rans-bucket-user1592923/data/US-immigrants/';
```
10. Load one partition into the table:
```sql
ALTER TABLE immigrants ADD PARTITION (year=2023, month=09) LOCATION 's3://rans-bucket-user1592923/data/US-immigrants/year=2023/month=09/';
```
11. see that there is only data from this partition and not from another year/month
12. Add all existing partitions:
```sql
MSCK REPAIR TABLE immigrants;
```
