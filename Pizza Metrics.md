
# 🍜 Case Study #2: Pizza Metrics

![image](https://github.com/user-attachments/assets/81eddaaf-72d4-4321-8c37-55591ae9f98e)


## 📌 Business Problem

Pizza Runner is Danny's new startup that combines pizza delivery with an Uber-style model.
He started it from his house, hired runners, and launched a mobile app to take orders.
This case study explores order and delivery data to find useful business insights.

## 📊 Provided Datasets

Because Danny had a few years of experience as a data scientist, he knew data would be key to growing his business.
He created an ERD to plan his database and now needs help cleaning the data and running some basic analysis.

** ERD Diagram*

![image](https://github.com/user-attachments/assets/672b1086-02ed-40d9-9bfe-5abb0c18c0b0)

## 🧼 Data Cleaning 

Update the existing columns with proper NULLs and remove unwanted text

Convert text values in distance and duration columns to numeric-friendly format


-- Cleaning customer_orders table

UPDATE customer_orders
SET exclusions = NULL
WHERE exclusions IN ('null', '');

UPDATE customer_orders
SET extras = NULL
WHERE extras IN ('null', '');


-- Cleaning runner_orders table

UPDATE runner_orders
SET pickup_time = NULL
WHERE pickup_time = 'null';

UPDATE runner_orders
SET duration = RTRIM(duration, ' minutes');

UPDATE runner_orders
SET duration = NULL
WHERE duration = 'null';

UPDATE runner_orders
SET distance = RTRIM(distance, ' km');

UPDATE runner_orders
SET distance = NULL
WHERE distance = 'null';


