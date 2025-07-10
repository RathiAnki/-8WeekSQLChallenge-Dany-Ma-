## 1. What are the top 3 products by total revenue before discount?

```sql
Select  top 3 p.product_name,Sum(s.qty*s.price)as revenue
from [balanced_tree].[Sales] s
join balanced_tree.product_details p
on s.prod_id=p.product_id
group by p.product_name
order by revenue desc
```
| product_name                    | revenue |
|--------------------------------|---------|
| Blue Polo Shirt - Mens         | 217683  |
| Grey Fashion Jacket - Womens   | 209304  |
| White Tee Shirt - Mens         | 152000  |

## 2. What is the total quantity, revenue and discount for each segment?
``` sql
Select  p.segment_name,Sum(qty)as total_qty, Sum(s.price*s.qty)as total_revenue,
cast(sum(s.price*s.qty*s.discount/100.0)as decimal(10,2)) total_discount
from [balanced_tree].[Sales] s
join balanced_tree.product_details p
on s.prod_id=p.product_id
group by p.segment_name

```
