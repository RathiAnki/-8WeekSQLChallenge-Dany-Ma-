# Before & After Analysis : Solution 

- This technique is usually used when we inspect an important event and want to inspect the impact before and after a certain point in time.
Taking the week_date value of 2020-06-15 as the baseline week where the Data Mart sustainable packaging changes came into effect.
We would include all week_date values for 2020-06-15 as the start of the period after the change and the previous week_date values would be before
Using this analysis approach - answer the following questions:

1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?

````sql
-- it gives the week_number of particular date

Select distinct week_number from clean_weekly_sales
where week_date ='2020-06-15'
and calendar_year =2020

with before_after_4weeks as(
 Select 
 sum(
 case when week_number between 21 and 24 then cast(Sales as bigint) end)as four_week_before,
  sum(
 case when week_number between 25 and 28 then cast(Sales as bigint) end)as four_week_after
 from clean_weekly_sales
 where week_number between 21 and 28
and calendar_year =2020 )

 Select *,four_week_after-four_week_before as actual_value, 
 cast((four_week_after-four_week_before)*100.0/four_week_before as decimal (4,2))as percent_variance
 from before_after_4weeks

````
| four_week_before | four_week_after | actual_value | percent_variance |
|------------------|-----------------|--------------|------------------|
| 2345878357       | 2318994169      | -26884188    | -1.15            |

2. What about the entire 12 weeks before and after?
 ````sql

with before_after_12weeks as(
 Select 
 sum(
 case when week_number between 13 and 24 then cast(sales as bigint) end)as before_packaging ,
  sum(
 case when week_number between 25 and 37 then cast(sales as bigint) end)as after_packaging
 from clean_weekly_sales
 where week_number between 13 and 37
and calendar_year =2020

 )

 Select *,after_packaging-before_packaging as actual_value, 
 cast((after_packaging-before_packaging)*100.0/before_packaging as decimal (4,2))as percent_variance
 from before_after_12weeks

````
| before_packaging | after_packaging | actual_value | percent_variance |
|------------------|-----------------|--------------|------------------|
| 7126273147       | 6973947753      | -152325394   | -2.14            |

3. How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?

 - 12 week period metrics
````sql
 with before_after_12weeks as(
 Select calendar_year,
 sum(
 case when week_number between 13 and 24 then cast(sales as bigint) end)as before_packaging ,
  sum(
 case when week_number between 25 and 37 then cast(sales as bigint) end)as after_packaging
 from clean_weekly_sales
 group by calendar_year
 )

 Select *,after_packaging-before_packaging as actual_value, 
 cast((after_packaging-before_packaging)*100.0/before_packaging as decimal (4,2))as percent_variance
 from before_after_12weeks
````
| calendar_year | before_packaging | after_packaging | actual_value | percent_varianc |
|---------------|------------------|-----------------|--------------|-----------------|
| 2019          | 6883386397       | 6862646103      | -20740294    | -0.30           |
| 2020          | 7126273147       | 6973947753      | -152325394   | -2.14           |
| 2018          | 6396562317       | 6500818510      | 104256193    | 1.63            |\

- 4 week period metrics


````sql
 with before_after_4weeks as(
 Select calendar_year,
 sum(
 case when week_number between 21 and 24 then cast(sales as bigint) end)as four_week_before,
  sum(
 case when week_number between 25 and 28 then cast(sales as bigint) end)as four_week_after
 from clean_weekly_sales
 group by calendar_year

 )

 Select *,four_week_after-four_week_before as actual_value, 
 cast((four_week_after-four_week_before)*100.0/four_week_before as decimal (4,2))as percent_variance
 from before_after_4weeks
````
| calendar_year | four_week_before | four_week_after | actual_value | percent_variance |
|---------------|------------------|-----------------|--------------|------------------|
| 2019          | 2249989796       | 2252326390      | 2336594      | 0.10             |
| 2020          | 2345878357       | 2318994169      | -26884188    | -1.15            |
| 2018          | 2125140809       | 2129242914      | 4102105      | 0.19             |

````
