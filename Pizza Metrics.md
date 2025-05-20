
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

-- Replace 'null' and empty strings with actual NULLs in exclusions

UPDATE customer_orders
SET exclusions = NULL
WHERE exclusions IN ('null', '');


-- Replace 'null' and empty strings with actual NULLs in extras
UPDATE customer_orders
SET extras = NULL
WHERE extras IN ('null', '');






## Question and Solution:
