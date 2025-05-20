# 🍜 Case Study #1: Danny's Diner 

![Danny's Diner](https://github.com/user-attachments/assets/18b9c403-3ff0-4c7e-b7ca-36d130537a5b)


## 📌 Business Problem

Danny owns a small Japanese restaurant and wants to analyze customer behavior using SQL. He needs insights into:
Customer spending habits
Visit frequency
Most popular menu items
Effectiveness of the loyalty program

## 📊 Provided Datasets
Danny has shared three datasets:
Sales – Records customer purchases with order dates and product IDs.
Menu – Maps product IDs to menu items and their prices.
Members – Tracks customers who joined the loyalty program.

** ERD Diagram*

![ERD_Diagram](https://github.com/user-attachments/assets/aa65554a-9793-4727-9d43-bc91d1d44a90)




## Question and Solution:

  
**1. What is the total amount each customer spent at the restaurant?

Select s.customer_id,Sum(m.price) as amount_spent from sales s
join menu m
on s.product_id =m.product_id
group by s.customer_id;

| customer_id | total_sales |
|------------|------------|
| A          | 76         |
| B          | 74         |
| C          | 36         |


**2. How many days has each customer visited the restaurant?

Select s.customer_id,Count(distinct order_date)as cutsomer_visit_day
from sales s
group by s.customer_id;

| customer_id | customer_visit_day |
|------------|-------------------|
| A          | 4                 |
| B          | 6                 |
| C          | 2                 |




**3. What was the first item from the menu purchased by each customer?

Select customer_id, product_name from (
Select s.customer_id,m.product_name,s.order_date,row_number()over(partition by s.customer_id order by s.order_date) as rn
from sales s
join menu m
on s.product_id =m.product_id)a
where rn =1;

| customer_id | product_name |
|------------|--------------|
| A          | sushi        |
| B          | curry        |
| C          | ramen        |



**4. What is the most purchased item on the menu and how many times was it purchased by all customers?

Select top 1 m.product_name , count(*) as total_purchses
from sales s
join menu m
on s.product_id =m.product_id
group by m.product_name
order by total_purchses desc;

| product_name | total_purchases |
|-------------|----------------|
| ramen       | 8              |



**5. Which item was the most popular for each customer?

Select customer_id ,product_name, cnt
from(
Select s.customer_id ,m.product_name, count(1)as cnt,dense_rank() over(partition  by customer_id order by count(1) desc)as rn
from sales s
join menu m
on s.product_id =m.product_id
group by s.customer_id ,m.product_name) a
where rn =1;

| customer_id | product_name | cnt |
|------------|-------------|-----|
| A          | ramen       | 3   |
| B          | sushi       | 2   |
| B          | curry       | 2   |
| B          | ramen       | 2   |
| C          | ramen       | 3   |




**6. Which item was purchased first by the customer after they became a member?

Select customer_id,product_name 
from (Select s.customer_id,m.product_name, s.order_date,row_number() over(partition by s.customer_id order by s.order_date)as rn
from sales s
 join menu m
 on s.product_id =m.product_id
 join members me
 on s.customer_id =me.customer_id
 where s.order_date >me.join_date) as first_join 
 where rn =1;


| customer_id | product_name |
|------------|-------------|
| A          | ramen       |
| B          | sushi       |


**7. Which item was purchased just before the customer became a member?

Select customer_id,product_name from (
Select s.customer_id,m.product_name, s.order_date,row_number() over(partition by me.customer_id order by s.order_date desc)as rn
from sales s
 join menu m
 on s.product_id =m.product_id
 join members me
 on s.customer_id =me.customer_id
 where s.order_date < me.join_date) a
 where rn =1;

| customer_id | product_name |
|------------|-------------|
| A          | sushi       |
| B          | sushi       |




**8. What is the total items and amount spent for each member before they became a member?

Select s.customer_id, count(1) as total_items, sum(m.price) as amount_spent
from sales  s
join  menu m
on s.product_id =m. product_id
 left join members  me
on s.customer_id =me.customer_id
where s.order_date < me.join_date
group by s.customer_id;

| customer_id | total_items | amount_spent |
|------------|------------|--------------|
| A          | 2          | 25           |
| B          | 3          | 40           |




**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
with cte as (
Select s.customer_id,m.product_id, m.price,
case when m.product_name ='sushi' then m.price *20 else m.price *10 end as point
from sales  s
join  menu m
on s.product_id =m. product_id
)

Select customer_id ,sum(point) as total_point
from cte
group by customer_id;

| customer_id | total_point |
|------------|------------|
| A          | 860        |
| B          | 940        |
| C          | 360        |


**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

Select s.customer_id,
sum(case when s.order_date between m.join_date and dateadd(day, 6, m.join_date) then me.price *20 else me.price *10 end )as points
from sales s
join members m
on s.customer_id =m.customer_id
join menu me
on s.product_id =me.product_id
where order_date <= '2021-01-31'
group by s.customer_id;

| customer_id | points |
|------------|--------|
| A          | 1270   |
| B          | 720    |


## Bonus Questions

**1: Join All The Things (customer_id,order_date,product_name, price, member(Y/N)

Select s.customer_id,s.order_date,m.product_name,m.price,
case when s.order_date >=me.join_date then 'Y' else 'N' end as member 
from sales s
join menu m
on s.product_id =m.product_id
left join members me
on s.customer_id =me.customer_id;

| customer_id | order_date | product_name | price | member |
|------------|------------|-------------|------|--------|
| A          | 2021-01-01 | sushi       | 10   | N      |
| A          | 2021-01-01 | curry       | 15   | N      |
| A          | 2021-01-07 | curry       | 15   | Y      |
| A          | 2021-01-10 | ramen       | 12   | Y      |
| A          | 2021-01-11 | ramen       | 12   | Y      |
| A          | 2021-01-11 | ramen       | 12   | Y      |
| B          | 2021-01-01 | curry       | 15   | N      |
| B          | 2021-01-02 | curry       | 15   | N      |
| B          | 2021-01-04 | sushi       | 10   | N      |
| B          | 2021-01-11 | sushi       | 10   | Y      |
| B          | 2021-01-16 | ramen       | 12   | Y      |
| B          | 2021-02-01 | ramen       | 12   | Y      |
| C          | 2021-01-01 | ramen       | 12   | N      |
| C          | 2021-01-01 | ramen       | 12   | N      |
| C          | 2021-01-07 | ramen       | 12   | N      |


--2:**Rank All The Things**

**Danny also requires further information about the ```ranking``` of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ```ranking``` values for the records when customers are not yet part of the loyalty program.**

Select * ,
case when member ='Y' then row_number() over(partition by customer_id, member order by order_date)  else null end as ranking from (
Select s.customer_id,s.order_date,m.product_name,m.price,
case when s.order_date >=me.join_date then 'Y' else 'N' end as member 
from sales s
join menu m
on s.product_id =m.product_id
left join members me
on s.customer_id =me.customer_id) ranking_data;


| customer_id | order_date  | product_name | price | member | ranking |
|------------|------------|--------------|-------|--------|---------|
| A          | 2021-01-01 | sushi        | 10    | N      | NULL    |
| A          | 2021-01-01 | curry        | 15    | N      | NULL    |
| A          | 2021-01-07 | curry        | 15    | Y      | 1       |
| A          | 2021-01-10 | ramen        | 12    | Y      | 2       |
| A          | 2021-01-11 | ramen        | 12    | Y      | 3       |
| A          | 2021-01-11 | ramen        | 12    | Y      | 4       |
| B          | 2021-01-01 | curry        | 15    | N      | NULL    |
| B          | 2021-01-02 | curry        | 15    | N      | NULL    |
| B          | 2021-01-04 | sushi        | 10    | N      | NULL    |
| B          | 2021-01-11 | sushi        | 10    | Y      | 1       |
| B          | 2021-01-16 | ramen        | 12    | Y      | 2       |
| B          | 2021-02-01 | ramen        | 12    | Y      | 3       |
| C          | 2021-01-01 | ramen        | 12    | N      | NULL    |
| C          | 2021-01-01 | ramen        | 12    | N      | NULL    |
| C          | 2021-01-07 | ramen        | 12    | N      | NULL    |



