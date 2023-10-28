
    Create S3 Role at AWS
    
![image](https://github.com/harishavenue1/S3-SnowFlake-Secured-Integration/assets/21108205/b45453f8-8657-461a-a5fb-f6f118fa9ab3)

    Create Storage Integration Object at Snowflake
    Provide ARN and S3 Bucket Details from AWS
    
![image](https://github.com/harishavenue1/S3-SnowFlake-Secured-Integration/assets/21108205/ca00aefc-8844-4424-916c-c0aaa07b7797)

    Describe the Storage Integration Object Created at Snowflake
    Collect ExternalID and UserARN Properties

![image](https://github.com/harishavenue1/S3-SnowFlake-Secured-Integration/assets/21108205/3edcfa1e-6228-48e0-b3b7-9906f477fb17)

    Update Details Collected to AWS Role
    
![Screenshot 2023-10-28 at 6 09 56 PM](https://github.com/harishavenue1/S3-SnowFlake-Secured-Integration/assets/21108205/5bab6062-b559-4fe7-88de-547f2279b397)

    Followed By Below Steps asusual to validate contents

-- New table

    CREATE TABLE orders_s3_init (
        order_id INTEGER PRIMARY KEY,
        user_id INTEGER,
        eval_set STRING,
        order_number INTEGER,
        order_dow INTEGER,
        order_hour_of_day INTEGER,
        days_since_prior_order INTEGER
    );

-- new file format

    CREATE OR REPLACE FILE FORMAT csv_file_format
    TYPE = 'CSV'
    FIELD_DELIMITER = ','
    SKIP_HEADER = 1
    FIELD_OPTIONALLY_ENCLOSED_BY = '"';

-- staging to aws s3

    CREATE STAGE my_stage_s3_init
    URL = "s3://snowflake-db-harish"
    STORAGE_INTEGRATION = s3_init
    FILE_FORMAT = (FORMAT_NAME = 'csv_file_format');

-- validate connection

    List @my_stage_s3_init;

-- copy data from s3

    COPY INTO orders_s3_init (order_id, user_id, eval_set, order_number, order_dow, order_hour_of_day, days_since_prior_order)
    FROM @my_stage_s3_init/instacart/orders.csv
    FILE_FORMAT = (FORMAT_NAME = 'csv_file_format')

-- Validate data loaded

    SELECT * FROM orders_s3_init LIMIT 10;
