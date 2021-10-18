# Dannys_Diner
![Unknown](https://user-images.githubusercontent.com/77989300/137805636-e0786535-6b1e-4f81-97fb-99b44894b527.jpeg)

## Goal Of Analysis

###### Strengthen the relationship between small-business owner and their target audience 
###### Extract and return valuable insights into customer spending behavior 
###### Answer business question for small-business owner 

## What was the total amount spent by each customer at this business? 
```sql
SELECT sales.customer_id, sales.order_date, sales.product_id, menu.product_name, menu.price, SUM(menu.price) OVER (
PARTITION BY sales.customer_id) AS customer_total
FROM dannys_diner.sales LEFT JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY sales.customer_id, sales.order_date, sales.product_id, menu.product_name, menu.price
ORDER BY sales.customer_id ASC; 
```
