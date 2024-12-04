# Regression with MindsDB

## Prerequisite for testing locally

1. Start MindsDB with [Docker](https://docs.mindsdb.com/setup/self-hosted/docker)

## Regression

### Database Engine



```sql
-- Connect to MindsDB demo database
CREATE DATABASE example_db
WITH ENGINE = "postgres",
PARAMETERS = {
"user": "demo_user",
"password": "demo_password",
"host": "samples.mindsdb.com",
"port": "5432",
"database": "demo",
"schema": "demo_data"
};

-- Check the existing rows
SELECT *
FROM example_db.home_rentals
LIMIT 10;

-- Display the amount of rows in the database
SELECT count(*)
FROM example_db.home_rentals;

-- Create Prediction Model
CREATE MODEL mindsdb.home_rentals_model
FROM example_db
(SELECT * FROM home_rentals)
PREDICT rental_price;

DESCRIBE home_rentals_model;

-- Make a prediction
SELECT rental_price,
rental_price_explain
FROM mindsdb.home_rentals_model
WHERE sqft = 823
AND location='good'
AND neighborhood='downtown'
AND days_on_market=10;

-- Create a job to do automatic learning every 2 days
CREATE JOB improve_model (

   RETRAIN mindsdb.home_rentals_model
   FROM example_db
         (SELECT * FROM home_rentals)

)
EVERY 2 days
IF (SELECT * FROM example_db.home_rentals
    WHERE created_at > LAST)

-- Compare if rental is a deal
SELECT t.rental_price as real_price,
ROUND((t.rental_price/m.rental_price)*100-100,2) as deal_pourcent,
t.number_of_rooms,  t.number_of_bathrooms, t.sqft, t.location, t.days_on_market
FROM example_db.home_rentals as t
JOIN mindsdb.home_rentals_model as m
LIMIT 10;

-- Try to predict # of rooms
SELECT number_of_rooms
FROM mindsdb.home_rentals_model
WHERE sqft = 823
AND location='good'
AND neighborhood='downtown'
AND days_on_market=10
AND real_price=1975;

-- Train a new model to predict # of rooms
CREATE MODEL mindsdb.home_rentals_model_rooms
FROM example_db
(SELECT * FROM home_rentals)
PREDICT number_of_rooms;

-- Ensure model is ready before moving forward
DESCRIBE home_rentals_model_rooms;

-- Make your prediction using the new model
SELECT number_of_rooms
FROM mindsdb.home_rentals_model_rooms
WHERE sqft = 823
AND location='good'
AND neighborhood='downtown'
AND days_on_market=10
AND real_price=1975;

```