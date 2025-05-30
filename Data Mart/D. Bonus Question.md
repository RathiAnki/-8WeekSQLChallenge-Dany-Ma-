# Bonus Question : Solution 

### Which areas of the business have the highest negative impact in sales metrics performance in 2020 for the 12 week before and after period?

 - region
````sql
with region_2020 as(Select region , 
sum(Case when week_number between 13 and 24 then cast(sales as bigint) end )as before_pacakging,
sum(Case when week_number between 25 and 37 then cast(sales as bigint) end )as after_pacakging
from clean_weekly_sales
where calendar_year =2020
group by region )

Select region ,	before_pacakging,after_pacakging,(after_pacakging-before_pacakging) as difference
,cast((after_pacakging-before_pacakging)*100.0/before_pacakging  as decimal(4,2)) as pct
from region_2020
order by pct desc
````
| region        | before_pacagking | after_pacagking | difference | pct   |
|---------------|------------------|-----------------|------------|-------|
| EUROPE        | 108886567        | 114038959       | 5152392    | 4.73  |
| AFRICA        | 1709537105       | 1700390294      | -9146811   | -0.54 |
| USA           | 677013558        | 666198715       | -10814843  | -1.60 |
| CANADA        | 426438454        | 418264441       | -8174013   | -1.92 |
| SOUTH AMERICA | 213036207        | 208452033       | -4584174   | -2.15 |
| OCEANIA       | 2354116790       | 2282795690      | -71321100  | -3.03 |
| ASIA          | 1637244466       | 1583807621      | -53436845  | -3.26 |

- platform
````sql
--platform
with platform_2020 as(Select platform , 
sum(Case when week_number between 13 and 24 then cast(sales as bigint) end )as before_pacakging,
sum(Case when week_number between 25 and 37 then cast(sales as bigint) end )as after_pacakging
from clean_weekly_sales
where calendar_year =2020
group by platform )

Select platform ,	before_pacakging,after_pacakging,(after_pacakging-before_pacakging) as difference
,cast((after_pacakging-before_pacakging)*100.0/before_pacakging  as decimal(4,2)) as pct
from platform_2020
order by pct desc
````
| platform | before_pacagking | after_pacagking | difference | pct  |
|----------|------------------|-----------------|------------|------|
| Shopify  | 219412034        | 235170474       | 15758440   | 7.18 |
| Retail   | 6906861113       | 6738777279      | -168083834 | -2.43|

- age_band
````sql
with age_band_2020 as(Select age_band , 
sum(Case when week_number between 13 and 24 then cast(sales as bigint) end )as before_pacakging,
sum(Case when week_number between 25 and 37 then cast(sales as bigint) end )as after_pacakging
from clean_weekly_sales
where calendar_year =2020
group by age_band )

Select age_band ,	before_pacakging,after_pacakging,(after_pacakging-before_pacakging) as difference
,cast((after_pacakging-before_pacakging)*100.0/before_pacakging  as decimal(4,2)) as pct
from age_band_2020
order by pct desc
````
| age_band     | before_pacagking | after_pacagking | difference | pct   |
|--------------|------------------|-----------------|------------|-------|
| Young Adults | 801806528        | 794417968       | -7388560   | -0.92 |
| Retirees     | 2395264515       | 2365714994      | -29549521  | -1.23 |
| Middle Aged  | 1164847640       | 1141853348      | -22994292  | -1.97 |
| unknown      | 2764354464       | 2671961443      | -92393021  | -3.34 |
  
- demographic
 ````sql
  with demographic_2020 as(Select demographic , 
sum(Case when week_number between 13 and 24 then cast(sales as bigint) end )as before_pacakging,
sum(Case when week_number between 25 and 37 then cast(sales as bigint) end )as after_pacakging
from clean_weekly_sales
where calendar_year =2020
group by demographic )

Select demographic ,	before_pacakging,after_pacakging,(after_pacakging-before_pacakging) as difference
,cast((after_pacakging-before_pacakging)*100.0/before_pacakging  as decimal(4,2)) as pct
from demographic_2020
order by pct desc
````
| demographic | before_pacagking | after_pacagking | difference | pct   |
|-------------|------------------|-----------------|------------|-------|
| Couples     | 2033589643       | 2015977285      | -17612358  | -0.87 |
| Families    | 2328329040       | 2286009025      | -42320015  | -1.82 |
| unknown     | 2764354464       | 2671961443      | -92393021  | -3.34 |

- customer_type
````sql
-- customer_type
with customer_type_2020 as(Select customer_type , 
sum(Case when week_number between 13 and 24 then cast(sales as bigint) end )as before_pacakging,
sum(Case when week_number between 25 and 37 then cast(sales as bigint) end )as after_pacakging
from clean_weekly_sales
where calendar_year =2020
group by customer_type )

Select customer_type ,	before_pacakging,after_pacakging,(after_pacakging-before_pacakging) as difference
,cast((after_pacakging-before_pacakging)*100.0/before_pacakging  as decimal(4,2)) as pct
from customer_type_2020
order by pct desc
````

| customer_type | before_pacagking | after_pacagking | difference | pct  |
|---------------|------------------|-----------------|------------|------|
| New           | 862720419        | 871470664       | 8750245    | 1.01 |
| Existing      | 3690116427       | 3606243454      | -83872973  | -2.27|
| Guest         | 2573436301       | 2496233635      | -77202666  | -3.00|

Do you have any further recommendations for Danny’s team at Data Mart or any interesting insights based off this analysis?

### Recommendations

- Focus more on Asia and Oceania where sales dropped, and try to adapt packaging to local tastes.

- Help retailers understand and promote the new packaging, while giving extra benefits to Shopify customers to keep them engaged.

- Clean up and improve data, especially for unknown customer groups, and create better campaigns for different age groups—like social media for young adults and offline for retirees.

- Give new customers welcome offers, reward loyal customers with discounts, and find out why guests aren’t coming back.

