# Data Exploration :Solution

1. What day of the week is used for each week_date value?
````sql

Select distinct Datename(weekday,week_date)as week_day
from clean_weekly_sales
````
| week_day |
|----------|
| Monday   |

2. What range of week numbers are missing from the dataset?
````sql
with range_of_week as(
Select 1 as week_num
union all 
select week_num +1
from range_of_week
where week_num<52)

select r.week_num as missing_week_range
from range_of_week r
left join clean_weekly_sales c
on r.week_num = c.week_number
where c.week_number is null
order by missing_week_range
````
| missing_week_range |
|--------------------|
| 1                  |
| 2                  |
| 3                  |
| 4                  |
| 5                  |
| 6                  |
| 7                  |
| 8                  |
| 9                  |
| 10                 |
| 11                 |
| 12                 |
| 37                 |
| 38                 |
| 39                 |
| 40                 |
| 41                 |
| 42                 |
| 43                 |
| 44                 |
| 45                 |
| 46                 |
| 47                 |
| 48                 |
| 49                 |
| 50                 |
| 51                 |
| 52                 |

3. How many total transactions were there for each year in the dataset?
````sql
Select calendar_year,count(1)as total_transactions
from clean_weekly_sales
group by calendar_year
order by calendar_year
````
| calendar_year | total_transactions |
|---------------|--------------------|
| 2018          | 5698               |
| 2019          | 5708               |
| 2020          | 5711               |

4. What is the total sales for each region for each month?
````sql
Select region, month_number,sum(cast (sales as bigint))as total_sales
from clean_weekly_sales
group by region,month_number
order by region,month_number
````
| region | month_number | total_sales |
|--------|--------------|-------------|
| AFRICA | 3            | 567767480   |
| AFRICA | 4            | 1911783504  |
| AFRICA | 5            | 1647244738  |
| AFRICA | 6            | 1767559760  |
| AFRICA | 7            | 1960219710  |
| AFRICA | 8            | 1809596890  |
| AFRICA | 9            | 276320987   |
| ASIA   | 3            | 529770793   |
| ASIA   | 4            | 1804628707  |
| ASIA   | 5            | 1526285399  |
| ASIA   | 6            | 1619482889  |
| ASIA   | 7            | 1768844756  |
| ASIA   | 8            | 1663320609  |
| ASIA   | 9            | 252836807   |

5.  What is the total count of transactions for each platform
````sql
Select platform,count(*)as total_transactions
from clean_weekly_sales
group by platform

````
| platform | total_transactions |
|----------|--------------------|
| Retail   | 8568               |
| Shopify  | 8549               |

6. What is the percentage of sales for Retail vs Shopify for each month?
````sql
with total_sales as (Select platform,calendar_year,month_number,sum(cast(sales as bigint))as total_sales
from clean_weekly_sales
group by platform,calendar_year,month_number)


Select calendar_year,month_number,
round(max(case when PLATFORM ='Retail' then total_sales end )*100.0/ sum(total_sales),2)
as retail_sales,
round(max(case when platform ='Shopify' then  total_sales end )*100.0/sum(total_sales),2)
as shopify_sales
from total_sales
group by calendar_year,month_number
order by calendar_year,month_number
````
| calendar_year | month_number | retail_sales         | shopify_sales        |
|---------------|--------------|----------------------|----------------------|
| 2018          | 3            | 97.92000000000000    | 2.08000000000000     |
| 2018          | 4            | 97.93000000000000    | 2.07000000000000     |
| 2018          | 5            | 97.73000000000000    | 2.27000000000000     |
| 2018          | 6            | 97.76000000000000    | 2.24000000000000     |
| 2018          | 7            | 97.71000000000000    | 2.29000000000000     |
| 2018          | 8            | 97.75000000000000    | 2.25000000000000     |
| 2018          | 9            | 97.68000000000000    | 2.32000000000000     |
| 2019          | 3            | 97.71000000000000    | 2.29000000000000     |
| 2019          | 4            | 97.80000000000000    | 2.20000000000000     |
| 2019          | 5            | 97.52000000000000    | 2.48000000000000     |
| 2019          | 6            | 97.42000000000000    | 2.58000000000000     |
| 2019          | 7            | 97.35000000000000    | 2.65000000000000     |
| 2019          | 8            | 97.21000000000000    | 2.79000000000000     |
| 2019          | 9            | 97.09000000000000    | 2.91000000000000     |
| 2020          | 3            | 97.30000000000000    | 2.70000000000000     |
| 2020          | 4            | 96.96000000000000    | 3.04000000000000     |
| 2020          | 5            | 96.71000000000000    | 3.29000000000000     |
| 2020          | 6            | 96.80000000000000    | 3.20000000000000     |
| 2020          | 7            | 96.67000000000000    | 3.33000000000000     |
| 2020          | 8            | 96.51000000000000    | 3.49000000000000     |

7. What is the percentage of sales by demographic for each year in the dataset?
````sql
with total_sales as(
Select calendar_year,demographic,sum(cast(sales as bigint)) as yearly_sales
from clean_weekly_sales
group by calendar_year,demographic)

Select calendar_year,
cast(max(case when demographic ='Families' then yearly_sales end )*100.0 / sum(yearly_sales)  as decimal(4,2)) as families_percent,
cast(max(case when demographic ='Couples' then yearly_sales end )*100.0 / sum(yearly_sales) as decimal(4,2)) as couples_percent,
cast(max(case when demographic ='unknown' then yearly_sales end )*100.0 / sum(yearly_sales) as decimal(4,2)) as unknown_percent
from total_sales
group by calendar_year

````
| calendar_year | families_percent | couples_percent | unknown_percent |
|---------------|------------------|-----------------|-----------------|
| 2018          | 31.99            | 26.38           | 41.63           |
| 2019          | 32.47            | 27.28           | 40.25           |
| 2020          | 32.73            | 28.72           | 38.55           |

8. Which age_band and demographic values contribute the most to Retail sales?
````sql
select age_band,demographic,
cast(sum(cast(sales as bigint))*100.0/
(select sum(cast(sales as bigint)) from clean_weekly_sales
where PLATFORM ='retail')as decimal (4,2))
as percent_contri
from clean_weekly_sales
where PLATFORM ='Retail'
group by age_band,demographic
order by percent_contri
````
| age_band     | demographic | percent_contri |
|--------------|-------------|----------------|
| Young Adults | Families    | 4.47           |
| Middle Aged  | Couples     | 4.68           |
| Young Adults | Couples     | 6.56           |
| Middle Aged  | Families    | 10.98          |
| Retirees     | Couples     | 16.07          |
| Retirees     | Families    | 16.73          |
| unknown      | unknown     | 40.52          |

- Unknown Demographic with unknown age_band contributes more in retail_sales

9. Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?
````sql
    select calendar_year,PLATFORM,
sum(cast(sales as bigint))as total_sales, sum(cast(transactions  as bigint))as total_transactions,
sum(cast(sales as bigint))/ sum(cast(transactions  as bigint))as Avg_transactions_size
from clean_weekly_sales
group by calendar_year,PLATFORM
order by calendar_year
````

| calendar_year | PLATFORM | total_sales | total_transactions | Avg_transactions_size |
|---------------|----------|-------------|--------------------|-----------------------|
| 2018          | Retail   | 12611171318 | 344919513          | 36                    |
| 2018          | Shopify  | 286209509   | 1486947            | 192                   |
| 2019          | Retail   | 13397806727 | 363740159          | 36                    |
| 2019          | Shopify  | 348225773   | 1899126            | 183                   |
| 2020          | Shopify  | 454582508   | 2539096            | 179                   |
| 2020          | Retail   | 13645638392 | 373274555          | 36                    |
