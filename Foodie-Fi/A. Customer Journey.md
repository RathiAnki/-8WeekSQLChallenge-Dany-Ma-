#  Customer Journey : Solution


Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.

Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!


![image](https://github.com/user-attachments/assets/5cc5cfdb-67b4-4cf5-bd42-fbae1dda85d3)

````sql
Select s.customer_id,s.plan_id,s.start_date
from plans p
join subscriptions s
on p.plan_id =s.plan_id
where customer_id in (1,2,11,1315,16,18,19)
````
| customer_id | plan_id | start_date  |
|-------------|---------|-------------|
| 1           | 0       | 2020-08-01  |
| 1           | 1       | 2020-08-08  |
| 2           | 0       | 2020-09-20  |
| 2           | 3       | 2020-09-27  |
| 11          | 0       | 2020-11-19  |
| 11          | 4       | 2020-11-26  |
| 16          | 0       | 2020-05-31  |
| 16          | 1       | 2020-06-07  |
| 16          | 3       | 2020-10-21  |
| 18          | 0       | 2020-07-06  |
| 18          | 2       | 2020-07-13  |
| 19          | 0       | 2020-06-22  |
| 19          | 2       | 2020-06-29  |
| 19          | 3       | 2020-08-29  |

# As per the data :

**Customer 1:** has continued using basic monthly plan through which he can only stream videos and have limited access.

**Customer 2:** has switched to annual plan for $199 where he has unlimited access and also can download videos.

**Customer 11:** has not opted for any plan and has cancelled the plan after trial.

**Customer 13:** has switched from basic monthly to pro monthly to have unlimited access and videos download facility.

**Customer 15**: has automatically sign-up for pro monthly after 7 days free trial which he cancelled afterwards.

**Customer 16:** has first opted for basic monthly, then switched to pro annual to take advantage of all the benefits associated with the plan.

**Customer 18:** is continuing with pro monthly plan and enjoy all the benefits comes with it.

**Customer 19:** has switched to pro annually.




