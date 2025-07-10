## 1. What was the total quantity sold for all products?

```sql
Select sum(qty)as total_qty_sold
from [balanced_tree].[Sales]

```
| total_qty_sold |
|----------------|
| 45216          |

## 2. What is the total generated revenue for all products before discounts?
```sql
Select sum(qty*price) as total_revenue
from [balanced_tree].[Sales] 
```
| total_revenue |
|----------------|
| 1289453        |

## 3. What was the total discount amount for all products?
```sql
Select round(sum(qty*price*discount/100.0),2) as total_discount
from [balanced_tree].[Sales] 
```
| total_discount |
|----------------|
| 156229.140000  |

