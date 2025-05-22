**- Data cleaning and adjustments**

````sql
-- For indvidually identify each order add column in customer_orders

Alter table customer_orders
add id int primary key identity(1,1)

-- Create temp table to split the comma seprated rows into individual rows

Select pizza_id,trim(value)as topping_id, t.topping_name
into #topping_list
from pizza_recipes r
cross apply string_split(toppings,',')as toppings_split
join pizza_toppings t
on t.topping_id =toppings_split.value


Select id  , trim(extra_split.value)as extras_id
into   #extratoppings
from customer_orders
cross apply string_split(extras ,',') as extra_split

Select id, trim(value)as exclusions
into    #exclusionstoppings
from customer_orders
cross apply string_split(exclusions ,',') as ex_split

-- To verify the adjustmenst made
Select * from #topping_list
Select * from #extratoppings
Select * from #exclusionstoppings
````

- Solution

--What are the standard ingredients for each pizza?
````sql
SELECT p.pizza_name, STRING_AGG(t.topping_name,',') as toppings
from #topping_list t
join pizza_names p
on t.pizza_id =p.pizza_id
group by p.pizza_name
````

| pizza_name | toppings                                                       |
|------------|----------------------------------------------------------------|
| Meatlovers | Bacon,BBQ Sauce,Beef,Cheese,Chicken,Mushrooms,Pepperoni,Salami |
| Vegetarian | Cheese,Mushrooms,Onions,Peppers,Tomatoes,Tomato Sauce          |



--What was the most commonly added extra?

````sql
select  top 1 t.topping_name, count(*) extras_toppings
from pizza_toppings t
join #extratoppings e
on t.topping_id =e.extras_id
group by t.topping_name
order by extras_toppings desc
````

| topping_name | extras_toppings |
|--------------|-----------------|
| Bacon        | 4               |

--What was the most common exclusion?
````sql
select  top 1 t.topping_name, count(*) exclusions_toppings
from pizza_toppings t
join #exclusionstoppings e
on t.topping_id =e.exclusions
group by t.topping_name
order by exclusions_toppings desc
````

| topping_name | exclusions_toppings |
|--------------|---------------------|
| Cheese       | 4                   |


--Generate an order item for each record in the customers_orders table in the format of one of the following:
--Meat Lovers
--Meat Lovers - Exclude Beef
--Meat Lovers - Extra Bacon
--Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

````sql

With extras_cte as(
Select e.id , 'Extra ' + String_agg(p.topping_name,',')as extra_topping
from #extratoppings e
join pizza_toppings p
on e.extras_id =p.topping_id
group by e.id)

, exclude_cte as(
Select e.id , 'Exclude ' + String_agg(p.topping_name,',')as exclude_topping
from #exclusionstoppings e
join pizza_toppings p
on e.exclusions =p.topping_id
group by e.id)


select order_id, customer_id,p.pizza_id,
case 
when ex.exclude_topping is null and e.extra_topping is null then p.pizza_name 
when ex.exclude_topping is null and e.extra_topping is not null  then  concat(p.pizza_name ,' - ',e.extra_topping) 
when ex.exclude_topping is not null and e.extra_topping is  null  then  concat(p.pizza_name ,' - ',ex.exclude_topping) 
else  concat(p.pizza_name ,' - ',ex.exclude_topping, ' - ',e.extra_topping) 
end as pp
from customer_orders c
join pizza_names p
on c.pizza_id =p.pizza_id
left join extras_cte e
on c.id =e.id
left join exclude_cte ex
on c.id =ex.id
````

| order_id | customer_id | pizza_id | exclusion_extras                                   |
|----------|-------------|----------|----------------------------------------------------|
| 1        | 101         | 1        | Meatlovers                                         |
| 2        | 101         | 1        | Meatlovers                                         |
| 3        | 102         | 1        | Meatlovers                                         |
| 3        | 102         | 2        | Vegetarian                                         |
| 4        | 103         | 1        | Meatlovers - Exclude Cheese                        |
| 4        | 103         | 1        | Meatlovers - Exclude Cheese                        |
| 4        | 103         | 2        | Vegetarian - Exclude Cheese                        |
| 5        | 104         | 1        | Meatlovers - Extra Bacon                           |
| 6        | 101         | 2        | Vegetarian                                         |
| 7        | 105         | 2        | Vegetarian - Extra Bacon                           |
| 8        | 102         | 1        | Meatlovers                                         |
| 9        | 103         | 1        | Meatlovers - Exclude Cheese - Extra Bacon,Chicken  |
| 10       | 104         | 1        | Meatlovers                                         |
| 10       | 104         | 1        | Meatlovers - Exclude BBQ Sauce,Mushrooms - Extra Bacon,Cheese |


**- could you help me with these two queries?**
````sql
--Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
--For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"


--What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?

````









