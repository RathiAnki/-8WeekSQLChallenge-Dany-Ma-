#  Customer Transaction : Solution 

- What is the unique count and total amount for each transaction type?
````sql
Select txn_type, count( txn_type) as unique_count, sum(txn_amount)as total_amount
from customer_transactions
group by txn_type
````
| txn_type  | unique_count | total_amount |
|-----------|--------------|--------------|
| withdrawal| 1580         | 793003       |
| deposit   | 2671         | 1359168      |
| purchase  | 1617         | 806537       |

- What is the average total historical deposit counts and amounts for all customers?
````sql
select avg(deposit_count)avg_deposit_count, avg(deposit_amount)avg_deposit_amount
from (
select customer_id ,count(1)as deposit_count, sum(txn_amount)as deposit_amount
from customer_transactions
where txn_type ='deposit'
group by customer_id 
)all_customer

````

| avg_deposit_count | avg_deposit_amount |
|-------------------|--------------------|
| 5                 | 2718               |

- For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
``` sql
select txn_month, count(distinct customer_id)customers_meeting_criteria from(
select customer_id, month(txn_date)txn_month,sum(case when txn_type ='deposit' then 1 else 0 end)as deposit_count,
sum(case when txn_type ='purchase' or txn_type ='withdrawal' then 1 else 0 end)as purchase_or_withdrawal_count
from customer_transactions
group by customer_id,month(txn_date))a
where deposit_count>1 
and purchase_or_withdrawal_count>=1
group by txn_month
order by txn_month;
````
| txn_month | customers_meeting_criteria |
|-----------|----------------------------|
| 1         | 168                        |
| 2         | 181                        |
| 3         | 192                        |
| 4         | 70                         |

- What is the closing balance for each customer at the end of the month?
```` sql
-  Filter the Txn_type
With recu_cte as(
Select customer_id, Eomonth(txn_date)as end_date,
Sum(Case when txn_type ='deposit' then txn_amount else -txn_amount end )as txn
from customer_transactions
group by customer_id,Eomonth(txn_date)
)

-  creating of rolling calendar to get then EOMonth of Jan to Apr
, rolling_calendar as(
Select   distinct customer_id,EOMONTH('2020-01-01')as month_end
from customer_transactions
union all
select customer_id,eomonth(DATEADD(month,1,month_end))as end_of_month
from rolling_calendar
where eomonth(DATEADD(month,1,month_end))<='2020-04-30')


-  Running total 
SELECT  r.customer_id,r.month_end,coalesce(c.txn,0)txn,
Sum(c.txn)over(partition by r.customer_id order by month_end rows between unbounded preceding and current row)closing_balance
    from rolling_calendar  r
	left join recu_cte c
	on r.customer_id=c.customer_id
	and c.end_date=r.month_end
````
### Note : result show only customer_id 1,2,3,4,5 as there are 2000 rows in total.

| customer_id | month_end  | txn   | closing_bal |
|-------------|------------|-------|-------------|
| 1           | 2020-01-31 | 312   | 312         |
| 1           | 2020-02-29 | 0     | 312         |
| 1           | 2020-03-31 | -952  | -640        |
| 1           | 2020-04-30 | 0     | -640        |
| 2           | 2020-01-31 | 549   | 549         |
| 2           | 2020-02-29 | 0     | 549         |
| 2           | 2020-03-31 | 61    | 610         |
| 2           | 2020-04-30 | 0     | 610         |
| 3           | 2020-01-31 | 144   | 144         |
| 3           | 2020-02-29 | -965  | -821        |
| 3           | 2020-03-31 | -401  | -1222       |
| 3           | 2020-04-30 | 493   | -729        |
| 4           | 2020-01-31 | 848   | 848         |
| 4           | 2020-02-29 | 0     | 848         |
| 4           | 2020-03-31 | -193  | 655         |
| 4           | 2020-04-30 | 0     | 655         |
| 5           | 2020-01-31 | 954   | 954         |
| 5           | 2020-02-29 | 0     | 954         |
| 5           | 2020-03-31 | -2... | -1923       |
| 5           | 2020-04-30 | -490  | -2413       |

- What is the percentage of customers who increase their closing balance by more than 5%?

````sql
With recu_cte as(
Select customer_id, Eomonth(txn_date)as end_date,
Sum(Case when txn_type ='deposit' then txn_amount else -txn_amount end)as txn
from customer_transactions
group by customer_id,Eomonth(txn_date)
)
, rolling_calendar as(
Select   distinct customer_id,EOMONTH('2020-01-01')as month_end
from customer_transactions
union all
select customer_id,eomonth(DATEADD(month,1,month_end))as end_of_month
from rolling_calendar
where eomonth(DATEADD(month,1,month_end))<='2020-04-30')

, closing_bal as(
SELECT  r.customer_id,r.month_end,COALESCE(c.txn, 0)as txn_amount,
Sum(c.txn)over(partition by r.customer_id order by month_end rows between unbounded preceding and current row)closing_balance
    from rolling_calendar  r
	left join recu_cte c
	on r.customer_id=c.customer_id
	and c.end_date=r.month_end)
	,previous_month as(
	Select * ,lead(closing_balance)over(partition by customer_id order by month_end)next_month_closing_bal
	from closing_bal)

	, percentage_increase as(
	Select *,
	(next_month_closing_bal-closing_balance)*100.0/nullif(closing_balance,0) as percent_growth
	from previous_month
	where closing_balance!=0 and next_month_closing_bal is not null)

	Select count(distinct customer_id)*100.0/(Select count(distinct customer_id) from customer_transactions)percentage_of_customer
	from percentage_increase
	where percent_growth >5
````
| percentage_of_customer |
|------------------------|
| 75.8000000000          |
