## Solution :  Pizza Metrics

**1. How many pizzas were ordered?**

````sql
Select count( order_id)as total_order
from customer_orders ;
````


| total_order |
|-------------|
|     14      |


**2.How many unique customer orders were made?**

````Sql
Select count( distinct order_id )as unique_customer
from customer_orders ;
````
| unique_customer |
|-----------------|
| 10              |



**3. How many successful orders were delivered by each runner?**
```sql
Select runner_id,count(1) as successful_orders
from runner_orders 
where cancellation is null
group by runner_id;
````
| runner_id | successful_orders |
|-----------|-------------------|
| 1         | 4                 |
| 2         | 3                 |
| 3         | 1                 |



**4. How many of each type of pizza was delivered?**
````sql
Select cast(p.pizza_name as varchar(20))as pizza_name,count(1) as pizza_count
from runner_orders r
join customer_orders c
on r.order_id=c.order_id
join pizza_names p
on c.pizza_id	=p.pizza_id
where cancellation is null
group by cast(p.pizza_name as varchar(20));

````

| pizza_name | pizza_count |
|------------|-------------|
| Meatlovers | 9           |
| Vegetarian | 3           |



**5. How many Vegetarian and Meatlovers were ordered by each customer?**
````sql

Select c.customer_id,cast(p.pizza_name as varchar(20))as pizza_name,count(1) as pizza_count
from customer_orders c
join pizza_names p
on c.pizza_id	=p.pizza_id
group by c.customer_id,cast(p.pizza_name as varchar(20))
order by c.customer_id;
````
| customer_id | pizza_name | pizza_count |
|-------------|------------|-------------|
| 101         | Meatlovers | 2           |
| 101         | Vegetarian | 1           |
| 102         | Meatlovers | 2           |
| 102         | Vegetarian | 1           |
| 103         | Meatlovers | 3           |
| 103         | Vegetarian | 1           |
| 104         | Meatlovers | 3           |
| 105         | Vegetarian | 1           |


**6. What was the maximum number of pizzas delivered in a single order?**
````sql
Select max(pizza_per_order)as max_pizza_delieverd from (
Select c.order_id, count(1)as pizza_per_order
from customer_orders c 
join runner_orders r
on c.order_id=r.order_id
where cancellation is null
group by c.order_id) Max_pizza;
````

| max_pizza_deliverd |
|--------------------|
| 3                  |


**7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?**

````Sql
Select c.customer_id,
	sum(case when c.exclusions <>'' or c.extras <>'' then 1 else 0 end) as changes_made,
	sum(case when c.exclusions ='' and c.extras ='' then 1 else 0 end) as changes_not_made
	from customer_orders c
	 join runner_orders r
	on c.order_id=r.order_id
	where r.cancellation is Null
	group by c.customer_id
````
| customer_id | changes_made | no_changes_made |
|-------------|--------------|-----------------|
| 101         | 0            | 2               |
| 102         | 0            | 3               |
| 103         | 3            | 0               |
| 104         | 2            | 1               |
| 105         | 1            | 0               |



**8. How many pizzas were delivered that had both exclusions and extras?**
````sql
Select count(c.pizza_id)as deliverd_pizza_with_exclusions_extras
from customer_orders c
join runner_orders r
on c.order_id=r.order_id
where r.pickup_time is  not null and 
 exclusions <>'' and extras <>''
````
| deliverd_pizza_with_exclusions_extras |
|---------------------------------------|
| 1                                     |



**9. What was the total volume of pizzas ordered for each hour of the day?**
````
Select datename(hh, order_time)as hour, Count(pizza_id)as pizza_count
from customer_orders
group by datename(hh, order_time)
order by hour;
````
| hour | pizza_count |
|------|-------------|
| 11   | 1           |
| 13   | 3           |
| 18   | 3           |
| 19   | 1           |
| 21   | 3           |
| 23   | 3           |


**10. What was the volume of orders for each day of the week?**

```` sql

Select datename(WEEKDAY, order_time)as week_day, Count(pizza_id)as pizza_count
from customer_orders
group by datename(WEEKDAY, order_time)
order by pizza_count desc;
````

| week_day | pizza_count |
|--------- |-------------|
| Saturday | 5           |
| Wednesday| 5           |
| Thursday | 3           |
| Friday   | 1           |

