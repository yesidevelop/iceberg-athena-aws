# Apache Iceberg on AWS

## Table of contents

- [What's included](#whats-included)
- [Set Up Work](#set-up)
- [Main Tutorial](#main-tutorial)
- [Useful Links](#useful-link)
- [Creators](#creators)

## What's included

The repo is to supplement the [youtube video](https://youtu.be/iGvj1gjbwl0) on Iceberg in AWS.

## Set up

1. Run the cloud formation template. This will create;
- The S3 bucket
- Glue database
- Athena work group

2. Upload the data from the `data` folder


## Main Tutorial
1. Upload the CSV data from `./data/csv`
2. Create the CSV table using athena
    ```
    CREATE EXTERNAL TABLE
      iceberg_tutorial_db.customer_csv
      (
            index_field bigint,
            customer_id string,
            first_name string,
            last_name string,
            company string,
            city string,
            country string,
            phone_1 string,
            phone_2 string,
            email string,
            subscription_date timestamp,
            website string
      )
      ROW FORMAT DELIMITED
      FIELDS TERMINATED BY ','
      STORED AS TEXTFILE
      LOCATION 's3://customer-data-iceberg/csv/';
    ```
3. Create an Apache Iceberg table
    ```
    CREATE TABLE
      iceberg_tutorial_db.customer_iceberg 
      (
            index_field bigint,
            customer_id string,
            first_name string,
            last_name string,
            company string,
            city string,
            country string,
            phone_1 string,
            phone_2 string,
            email string,
            subscription_date timestamp,
            website string
      )
      PARTITIONED BY (day(subscription_date))
      LOCATION 's3://customer-data-iceberg/customer_iceberg/'
      TBLPROPERTIES ( 'table_type' ='ICEBERG'  );
    ```

6. Run a query to look at the Day partition to see how Iceberg works
    ```
    SELECT * FROM customer_iceberg WHERE day(subscription_date) =  24 limit 20;
    ```
8. Update some data to see the change take place 
    ```
    UPDATE customer_iceberg SET company = 'Company2022' WHERE year(subscription_date) =2022;
    ```
9. Select the updated data
    ```
    SELECT * FROM customer_iceberg WHERE year(subscription_date) =2022 limit 10;
    ```
10. Time travel query
   
    New Query with Athena version 3
    ```
    SELECT * FROM customer_iceberg FOR TIMESTAMP AS OF (current_timestamp - interval '1' minute)
    WHERE year(subscription_date) =2022 limit 10;
    ```
11. Delete from iceberg table
    ```
    DELETE FROM customer_iceberg WHERE year(subscription_date) != 2008; 
    ```

## Creators

**Johnny Chivers**

- <https://github.com/johnny-chivers/>

## Useful Links

- [youtube video](https://youtu.be/iGvj1gjbwl0) 
- [website](https://www.johnnychivers.co.uk)
- [buy me a coffee](https://www.buymeacoffee.com/johnnychivers)


Enjoy :metal:
