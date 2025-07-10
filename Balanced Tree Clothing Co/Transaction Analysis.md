## 1. --How many unique transactions were there?

```sql
Select Count(distinct txn_id) as unique_txn
from [balanced_tree].[Sales]
```

| unique_txn |
|------------|
| 2500       |

## 2. What is the average unique products purchased in each transaction?

```sql
select  Avg(unique_prod)as avg_unique_products_per_txn
from (
Select  txn_id,count(distinct prod_id) as unique_prod
from [balanced_tree].[Sales]
group by txn_id)a
```
| avg_unique_products_per_txn |
|-----------------------------|
| 6                           |


## 3. What are the 25th, 50th and 75th percentile values for the revenue per transaction?

```sql
with cte as(
Select txn_id,
sum(qty*price *(1-discount/100.0))as revenue
from [balanced_tree].[Sales]
group by txn_id
)

Select 
distinct 
PERCENTILE_CONT(0.25)within group (order by revenue)over()as percentile_25,
PERCENTILE_CONT(0.50)within group (order by revenue)over()as percentile_50,
PERCENTILE_CONT(0.75)within group (order by revenue)over()as percentile_75
from cte
```


## 4. What is the average discount value per transaction?
```sql
Select cast(Avg(discount_amount)as decimal(4,2))as avg_discount from (
Select txn_id,
Sum(qty*price*discount/100.0)as discount_amount
from [balanced_tree].[Sales]
group by txn_id)a
```

| avg_discount |
|--------------|
| 62.49        |


## 5. What is the percentage split of all transactions for members vs non-members?

```sql
Select Round((members*100.0/total_txn),2)as members_percent,
round((non_members*100.0/total_txn),2) as non_members_percent
from(
Select count( txn_id)total_txn,
Sum( case when member ='t' then 1 else 0 end )as members,
Sum( case when member ='f' then 1 else 0 end )as non_members
from [balanced_tree].[Sales]
)a
```

| members_percent | non_members_percent |
|-----------------|---------------------|
| 60.03           | 39.97               |

## 6. What is the average revenue for member transactions and non-member transactions?

```sql
Select member,avg(total_revenue) as avg_revenue from (
Select member,txn_id,Sum(price*qty)as total_revenue
 from [balanced_tree].[Sales] 
 group by member,txn_id)a
 group by a.member
```
| member | avg_revenue |
|--------|-------------|
| f      | 515         |
| t      | 516         |



