
#  Runner and Customer Experience : Solution

--How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

````sql
Select datediff(week,'2021-01-01',registration_date)+1as registraion_week,count(runner_id)as no_of_runners
from runners
group by datediff(week,'2021-01-01',registration_date)+1;
````
|registraion_week|no_of_runners  |
|-------------|---------------|
| 1           | 1             |
| 2           | 2             |
| 3           | 1             |

--What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
````sql
With cte as(
Select r.runner_id,c.order_id,c.order_time,r.pickup_time , DATEDIFF(minute,c.order_time,r.pickup_time)as minute_diff
from runner_orders r
join customer_orders c
on r.order_id=c.order_id
where r.pickup_time is not null )

Select runner_id,avg(minute_diff) as avg_pickup_time
from cte
group by runner_id;
````
| runner_id | avg_pickup_time |
|-----------|-----------------|
| 1         | 15              |
| 2         | 24              |
| 3         | 10              |

--Is there any relationship between the number of pizzas and how long the order takes to prepare?
````sql
with cte as(
Select c.order_id ,count(c.order_id) as pizza_count, c.order_time, r.pickup_time,DATEDIFF(minute, c.order_time,r.pickup_time) as prep_time
from runner_orders r
join customer_orders c
on r.order_id=c.order_id
where r.cancellation is null
group by c.order_id,c.order_time, r.pickup_time)

Select pizza_count,avg(prep_time)as avg_time_to_prep_min
from cte
group by pizza_count;
````
| pizza_count | avg_time_to_prep_min |
|-------------|----------------------|
| 1           | 12                   |
| 2           | 18                   |
| 3           | 30                   |

Yes, If there is one pizza to deliver it takes avg time of around 12 mins to prepare ,as pizza count increases the prepartion time also increases.

--What was the average distance travelled for each customer?
````sql
 
 Select c.customer_id,round(avg(r.distance),2)as avg_distance_travelled
	from runner_orders r
	join customer_orders c
	on r.order_id=c.order_id
	where r.distance !=0
	group by c.customer_id;
````
| customer_id | avg_distance_travelled |
|-------------|------------------------|
| 101         | 20                     |
| 102         | 16.73                  |
| 103         | 23.4                   |
| 104         | 10                     |
| 105         | 25                     |

--What was the difference between the longest and shortest delivery times for all orders?
````sql
	Select max(duration)-min(duration)as delivery_time_range
	from runner_orders r
	where r.distance !=0;
````
| delivery_time_range |
|---------------------|
| 30                  |


--What was the average speed for each runner for each delivery and do you notice any trend for these values?
````sql
Select  order_id ,runner_id, round(avg( distance / (duration / 60.0)),2)AS avg_speed_kmh
from runner_orders
where distance !=0
group by order_id,runner_id
order by order_id;
````
| order_id | runner_id | avg_speed_kmh |
|----------|-----------|---------------|
| 1        | 1         | 37.5          |
| 2        | 1         | 44.44         |
| 3        | 1         | 40.2          |
| 4        | 2         | 35.1          |
| 5        | 3         | 40            |
| 7        | 2         | 60            |
| 8        | 2         | 93.6          |
| 10       | 1         | 60            |

** Data Interpretation**

- Runner 1 : Average speed lies between 37.5 to 60km/h.

- Runner 2 : Average speed lies between 35 to 60km/h, but it drastically change for Order_id 8 where the avg speed was 93.6km/h, the reason might be over speeding

- Runner 3 : Average speed of 40km/h , there is no enough data point to analyze trend.


--What is the successful delivery percentage for each runner?
```sql
with cte as(
Select runner_id,
  count(case when cancellation  is null then 1 end )as deliver_count,
 count(1) as total_delivery
from runner_orders
group by runner_id
)

select runner_id,
100*deliver_count/total_delivery*1.0 as successful_delivery_percent
from cte
````
| runner_id | successful_delivery_percent |
|-----------|-----------------------------|
| 1         | 100.0                       |
| 2         | 75.0                        |
| 3         | 50.0                        |

