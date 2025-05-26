# Data Analysis : Solution 


1. How many customers has Foodie-Fi ever had?

````sql
Select count(distinct customer_id)customer_count from 
subscriptions

````
| customer_count |
|---------------:|
|           1000 |

2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value.
````sql
Select format(start_date , 'MMMM')as monthly_start_date,count(1)as trial_plan_subscription
from subscriptions
where plan_id =0
group by format(start_date , 'MMMM') 
order by trial_plan_subscription 
````
| monthly_start_date | trial_plan_subscription |
|--------------------|------------------------:|
| February           |                      68 |
| November           |                      75 |
| October            |                      79 |
| June               |                      79 |
| April              |                      81 |
| December           |                      84 |
| September          |                      87 |
| May                |                      88 |
| August             |                      88 |
| January            |                      88 |
| July               |                      89 |
| March              |                      94 |


3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name.
````sql
Select p.plan_name ,count(*)as num_of_events
from plans p
join subscriptions s
on p.plan_id =s.plan_id
where year(s.start_date )=2021
group by p.plan_name
order by num_of_events
````
| plan_name     | num_of_events |
|--------------|--------------:|
| basic monthly|             8 |
| pro monthly  |            60 |
| pro annual   |            63 |
| churn        |            71 |



4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
````sql
Select count(distinct customer_id)as total_customer ,
cast(round(sum(case when plan_id =4 then 1 else 0 end)*100.0/count(distinct customer_id),2)as decimal(4,2)) as churned_customer_percentage
from subscriptions
````
|total_customer| churned_customer_percentage |
|--------------|-----------------------------|
| 1000         |            30.70|



5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
````sql
with cte as(
select s.customer_id, max(case when p.plan_name ='trial' then s.start_date end) as trial_plan,
max(case when p.plan_name ='churn' then s.start_date end) as churned
from subscriptions s
join plans p
on s.plan_id =p.plan_id
group by s.customer_id 

)

select count(customer_id)as  churned_customer_count, 
cast(round(count(customer_id)*100.0/(select count(distinct customer_id) from subscriptions),2)as decimal(4,1))as churn_percentage
from cte
where datediff(day,trial_plan,churned) =7


````
|churned_customer_count| churn_percentage |
|--------------|-----------------------------|
| 92         |            9.2|

6. What is the number and percentage of customer plans after their initial free trial?
````sql
with next_plan_cte as(
select s.customer_id,p.plan_name,s.start_date,lead(p.plan_name)over(partition by s.customer_id order by p.plan_id)as next_plan
from subscriptions s
join plans p
on s.plan_id =p.plan_id)

select next_plan,count(1) as customer_plan,
count(1)*100.0/(Select count(distinct customer_id) from subscriptions)
from next_plan_cte
where next_plan is not null
and plan_name ='trial'
group by next_plan


````
| next_plan     | customer_plan | percentage          |
|---------------|--------------:|--------------------:|
| basic monthly |           546 | 54.600000000000     |
| churn         |            92 | 9.200000000000      |
| pro annual    |            37 | 3.700000000000      |
| pro monthly   |           325 | 32.500000000000     |


7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?

````sql

with cte as(
Select customer_id,plan_id,start_date,lead(start_date)over(partition by customer_id order by start_date) as next_date
from subscriptions
where start_date <='2020-12-31'
)

select plan_id, count(distinct customer_id) as customer_count,
cast(round( count(distinct customer_id)*100.0 /(select count(distinct customer_id) from subscriptions),2) as decimal(4,2))as percentage_breakdown
from cte
where next_date is null
group by plan_id 
````
| plan_id | customer_count | percentage_breakdown |
|--------:|---------------:|--------------------:|
|       0 |             19 |                 1.90 |
|       1 |            224 |                22.40 |
|       2 |            326 |                32.60 |
|       3 |            195 |                19.50 |
|       4 |            236 |                23.60 |




8. How many customers have upgraded to an annual plan in 2020?
````sql
select count(distinct s.customer_id)as customer_count
from subscriptions s
join plans p
on s.plan_id =p.plan_id
where year(s.start_date ) =2020
and p.plan_name like '%annual'
````

  customer_count 
  |--------:|
  |       195 | 

9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?

````sql
with trial_plan as(
Select customer_id,start_date as trial_date
from subscriptions
where plan_id =0)
, annual_plan as (Select customer_id,start_date as annual_date
from subscriptions
where plan_id =3)

select avg(datediff(day,trial_date,annual_date))as avg_days
from trial_plan t
join annual_plan a
on t.customer_id =a.customer_id
````
  avg_days 
  |--------:|
  |       104 | 


10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
````sql
with trial_plan as(
Select customer_id,start_date as trial_date
from subscriptions
where plan_id =0)
, annual_plan as (Select customer_id,start_date as annual_date
from subscriptions
where plan_id =3)
,days_diff as(
select t.customer_id, datediff(day,trial_date,annual_date)as days_to_annual
from trial_plan t
join annual_plan a
on t.customer_id =a.customer_id
)

select 
 CASE 
    WHEN days_to_annual BETWEEN 0 AND 30 THEN '0–30 days'
    WHEN days_to_annual BETWEEN 31 AND 60 THEN '31–60 days'
    WHEN days_to_annual BETWEEN 61 AND 90 THEN '61–90 days'
    WHEN days_to_annual BETWEEN 91 AND 120 THEN '91–120 days'
    WHEN days_to_annual BETWEEN 121 AND 150 THEN '121–150 days'
    ELSE '150+ days' end as days_bin,
	count(1)as customer_count
from days_diff
group by CASE 
    WHEN days_to_annual BETWEEN 0 AND 30 THEN '0–30 days'
    WHEN days_to_annual BETWEEN 31 AND 60 THEN '31–60 days'
    WHEN days_to_annual BETWEEN 61 AND 90 THEN '61–90 days'
    WHEN days_to_annual BETWEEN 91 AND 120 THEN '91–120 days'
    WHEN days_to_annual BETWEEN 121 AND 150 THEN '121–150 days'
	ELSE '150+ days' end

````

| days_bin   | customer_count |
|------------|---------------:|
| 0–30 days  |             49 |
| 31–60 days |             24 |
| 61–90 days |             34 |
| 91–120 days|             35 |
| 121–150 days|            42 |
| 150+ days  |             74 |


11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
````sql
with cte as(
select
customer_id,start_date,plan_id,lead(plan_id )over(partition by customer_id order by start_date)as next_plan_id
from subscriptions
where plan_id in (2,1)
and year(start_date) =2020

)
select count(1)as customer_count
from cte
where next_plan_id =1
````

| customer_count |
|---------------:|
|              0 |

