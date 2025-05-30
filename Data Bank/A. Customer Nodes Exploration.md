# Customer Nodes Exploration : Solution 

- How many unique nodes are there on the Data Bank system?
```` sql
Select count(distinct node_id ) as unique_nodes from customer_nodes
````
| unique_nodes |
|--------------|
| 5            |

- What is the number of nodes per region?
```sql
Select r.region_name,count(distinct c.node_id)as node_count
from customer_nodes c
join regions r
on c.region_id=r.region_id
group by r.region_name
```
| region_name | node_count |
|-------------|------------|
| Africa      | 5          |
| America     | 5          |
| Asia        | 5          |
| Australia   | 5          |
| Europe      | 5          |

- How many customers are allocated to each region?
````sql
Select r.region_name,count(distinct c.customer_id)as customer_count
from customer_nodes c
join regions r
on c.region_id=r.region_id
group by r.region_name
````
| region_name | customer_count |
|-------------|----------------|
| Africa      | 102            |
| America     | 105            |
| Asia        | 95             |
| Australia   | 110            |
| Europe      | 88             |
- How many days on average are customers reallocated to a different node?
````sql
with cte as(
 select customer_id,node_id,start_date,end_date,
 datediff(day,start_date,end_date)node_days_difference
 from customer_nodes
 where end_date <'9999-12-31'
 group by customer_id,node_id,start_date,end_date
 )
 ,cte_2 as(
 select customer_id,node_id,sum(node_days_difference)total_difference
 from cte
 group by customer_id,node_id)

 Select round(avg(total_difference),2)as avg_days_between_node_reallocation from cte_2
````
| avg_days_between_node_reallocation |
|------------------------------------|
| 23                                 |

- What is the median, 80th and 95th percentile for this same reallocation days metric for each region?

````sql
with cte as(
 select customer_id,node_id,region_id,start_date,end_date,
 datediff(day,start_date,end_date)node_days_difference
 from customer_nodes
 where end_date <'9999-12-31'
 group by customer_id,node_id,region_id,start_date,end_date
 )
 ,cte_2 as(
 select customer_id,node_id,region_id,sum(node_days_difference)total_difference
 from cte
 group by customer_id,node_id,region_id)

Select distinct r.region_name,
PERCENTILE_CONT(0.5)within group (order by total_difference)over(partition by r.region_name)as median,
PERCENTILE_CONT(0.8)within group (order by total_difference)over(partition by r.region_name)as percentile_80,
PERCENTILE_CONT(0.95)within group (order by total_difference)over(partition by r.region_name) as percentile_95
from cte_2 c
join regions r
on c.region_id =r.region_id
````
| region_name | median | percentile_80 | percentile_95 |
|-------------|--------|---------------|---------------|
| Africa      | 22     | 35            | 54            |
| Asia        | 22     | 34.6          | 52            |
| Europe      | 23     | 34            | 51.4          |
| Australia   | 21     | 34            | 51            |
| America     | 22     | 34            | 53.7          |
