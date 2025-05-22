# Pricing and Ratings : Solution


--If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - 
--how much money has Pizza Runner made so far if there are no delivery fees?
````sql

Select
sum(case when p.pizza_name='Meatlovers' then 12 else 10 end) as money_made_by_pizza_runner
from customer_orders c
join 
runner_orders r
on c.order_id =r.order_id
join pizza_names p
on c.pizza_id=p.pizza_id
where r.distance  is not null
````
| money_made_by_pizza_runner |
|-------                     |
| 138                        |

--What if there was an additional $1 charge for any pizza extras?
--Add cheese is $1 extra
````sql
declare @pizza_total int = 138

Select  @pizza_total+
sum(case when p.topping_name ='Cheese' then 2  else 1 end )as extras_price
from #extratoppings e
join pizza_toppings p
on e.extras_id =p.topping_id
````
| extras_price |
|--------------|
| 145          |


--The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, 
--how would you design an additional table for this new dataset -
--generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.

````sql
create table ratings 
(order_id int,
rating int)

insert into ratings 
values
		(1,4),
		(2,5),
		(3,3),
		(4,5),
		(5,5),
		(7,3),
		(8,1),
		(10,5)

	-- Verify
	Select * from ratings

````
| order_id | rating |
|----------|--------|
| 1        | 4      |
| 2        | 5      |
| 3        | 3      |
| 4        | 5      |
| 5        | 5      |
| 7        | 3      |
| 8        | 1      |
| 10       | 5      |


--Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
--customer_id
--order_id
--runner_id
--rating
--order_time
--pickup_time
--Time between order and pickup
--Delivery duration
--Average speed
--Total number of pizzas

````sql
Select c.customer_id,c.order_id, r.runner_id,ra.rating,c.order_time,r.pickup_time ,datediff(minute,c.order_time,r.pickup_time)as time_diff,
r.duration,round(avg(r.distance/(r.duration/60.0)),2)as avg_speed,count(c.pizza_id) as total_pizza
from customer_orders c
join runner_orders r
on c.order_id=r.order_id
join ratings ra
on c.order_id =ra.order_id
where r.cancellation is null
group by c.customer_id,c.order_id, r.runner_id,ra.rating,c.order_time,r.pickup_time,r.duration

````
| customer_id | order_id | runner_id | rating | order_time          | pickup_time         | time_diff | duration | avg_speed | total_pizza |
|-------------|----------|-----------|--------|---------------------|---------------------|-----------|----------|-----------|-------------|
| 101         | 1        | 1         | 4      | 2020-01-01 18:05:02 | 2020-01-01 18:15:34 | 10        | 32       | 37.5      | 1           |
| 101         | 2        | 1         | 5      | 2020-01-01 19:00:52 | 2020-01-01 19:10:54 | 10        | 27       | 44.44     | 1           |
| 102         | 3        | 1         | 3      | 2020-01-02 23:51:23 | 2020-01-03 00:12:37 | 21        | 20       | 40.2      | 2           |
| 102         | 8        | 2         | 1      | 2020-01-09 23:54:33 | 2020-01-10 00:15:02 | 21        | 15       | 93.6      | 1           |
| 103         | 4        | 2         | 5      | 2020-01-04 13:23:46 | 2020-01-04 13:53:03 | 30        | 40       | 35.1      | 3           |
| 104         | 5        | 3         | 5      | 2020-01-08 21:00:29 | 2020-01-08 21:10:57 | 10        | 15       | 40        | 1           |
| 104         | 10       | 1         | 5      | 2020-01-11 18:34:49 | 2020-01-11 18:50:20 | 16        | 10       | 60        | 2           |
| 105         | 7        | 2         | 3      | 2020-01-08 21:20:29 | 2020-01-08 21:30:45 | 10        | 25       | 60        | 1           |



--If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and 
--each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?
````sql

with pizza_price as(
Select c.order_id ,
sum(case when p.pizza_name='Meatlovers' then 12 else 10 end) as price
from customer_orders c
join 
runner_orders r
on c.order_id =r.order_id
join pizza_names p
on c.pizza_id=p.pizza_id
where r.distance  is not null
group by c.order_id )

, km_amount as(
select order_id ,sum(distance*.30) price_km
	from runner_orders r
	group by order_id)

	Select sum(p.price) -sum(a.price_km) as amount_diff
	from pizza_price p 
	join km_amount a
	on p.order_id =a.order_id

````
| amount_diff |
|-------------|
| 94.44       |

# Bonus Question 

-If Danny wants to expand his range of pizzas - how would this impact the existing data design?
Write an INSERT statement to demonstrate what would happen if a new Supreme pizza with all the toppings was added to the Pizza Runner menu?
````sql
	insert into pizza_names values(3,'Supreme Pizza')
	insert into pizza_recipes values (3,'1,2,3,4,5,6,7,8,9,10,11,12')
````
-- Pizza_Name
| pizza_id | pizza_name    |
|----------|---------------|
| 1        | Meatlovers    |
| 2        | Vegetarian    |
| 3        | Supreme Pizza |

-- Pizza_recipes

| pizza_id | toppings                   |
|----------|----------------------------|
| 1        | 1, 2, 3, 4, 5, 6, 8, 10    |
| 2        | 4, 6, 7, 9, 11, 12         |
| 3        | 1,2,3,4,5,6,7,8,9,10,11,12 |



