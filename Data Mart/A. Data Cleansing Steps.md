# Data Cleansing Steps : Solution

### In a single query, perform the following operations and generate a new table in the data_mart schema named clean_weekly_sales:

- Convert the week_date to a DATE format

- Add a week_number as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc

- Add a month_number with the calendar month for each week_date value as the 3rd column

- Add a calendar_year column as the 4th column containing either 2018, 2019 or 2020 values

- Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value

![image](https://github.com/user-attachments/assets/2d3e7607-8068-47b4-93ea-bf3ba3b82fdb)

- Add a new demographic column using the following mapping for the first letter in the segment values:
segment	demographic
![image](https://github.com/user-attachments/assets/951338fe-0e4c-4f33-8e9c-d75f2477e787)

- Ensure all null string values with an "unknown" string value in the original segment column as well as the new age_band and demographic columns

- Generate a new avg_transaction column as the sales value divided by transactions rounded to 2 decimal places for each record
````sql
Select
  distinct CONVERT(DATE, week_date, 3)as week_date
  ,DATEPART(ww,CONVERT(DATE, week_date, 3))as week_number ,
  Month(CONVERT(DATE, week_date, 3))as month_number,
  Year(CONVERT(DATE, week_date, 3))as calendar_year,
  region,platform,isnull(nullif(cast(segment as varchar(10)),'null'), 'unknown')as segment
  ,coalesce(case
  when segment like '%1' then 'Young Adults'
  when segment like '%2' then  'Middle Aged'
  when  segment like '%3' or segment like '%4' then 'Retirees'
  end,'unknown') as age_band
  , coalesce(case 
  when  segment like 'C%' then 'Couples'
  when segment like 'F%' then 'Families'
  end ,'unknown')as demographic,
  customer_type ,
  transactions ,sales,
  round((sales/transactions ),2)as avg_transaction 
into clean_weekly_sales
from weekly_sales


Select * from   clean_weekly_sales

````
- there are total 17,117 rows

| week_date  | week_number | month_number | calendar_year | region | platform | segment | age_band    | demographic | customer_type | transactions | sales     | avg_bill_value |
|------------|-------------|--------------|---------------|--------|----------|---------|-------------|-------------|---------------|--------------|-----------|----------------|
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | C1      | Young Adults| Couples     | Existing      | 149334       | 6070949   | 40             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | C1      | Young Adults| Couples     | New           | 91097        | 2428945   | 26             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | C2      | Middle Aged | Couples     | Existing      | 82495        | 386146    | 47             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | C2      | Middle Aged | Couples     | New           | 51075        | 1692383   | 33             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | C3      | Retirees    | Couples     | Existing      | 218516       | 12083475  | 55             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | C3      | Retirees    | Couples     | New           | 98342        | 3706066   | 37             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | C4      | Retirees    | Couples     | Existing      | 83592        | 4672749   | 55             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | C4      | Retirees    | Couples     | New           | 39979        | 1440641   | 36             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | F1      | Young Adults| Families    | Existing      | 85585        | 526054    | 58             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | F1      | Young Adults| Families    | New           | 23569        | 905823    | 38             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | F2      | Middle Aged | Families    | Existing      | 221292       | 12966045  | 58             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | F2      | Middle Aged | Families    | New           | 61001        | 2316915   | 37             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | F3      | Retirees    | Families    | Existing      | 308162       | 18631509  | 50             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | F3      | Retirees    | Families    | New           | 61622        | 238435    | 38             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | unknown | unknown     | unknown     | New           | 28284        | 1340918   | 47             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | unknown | unknown     | unknown     | Guest         | 1620731      | 45990717  | 28             |
| 2018-03-26 | 13          | 3            | 2018          | AFRICA | Retail   | unknown | unknown     | unknown     | Existing      | 61875        | 2567272   | 41             |
