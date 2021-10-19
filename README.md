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

## How many total days has each customer visited this business? 
```sql
SELECT DISTINCT customer_id AS customer, COUNT(order_date) OVER (
PARTITION BY customer_id) AS days_visited 
FROM dannys_diner.sales 
GROUP BY customer_id, order_date
ORDER BY days_visited DESC;
```

## What was the first item purchased by each customer
```sql
SELECT sales.customer_id, MIN(sales.order_date), sales.product_id, menu.product_name
FROM dannys_diner.sales LEFT JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY sales.customer_id, sales.product_id, menu.product_name;
```
| customer\_id | min                      | product\_id | product\_name |
| ------------ | ------------------------ | ----------- | ------------- |
| B            | 2021-01-04T00:00:00.000Z | 1           | sushi         |
| A            | 2021-01-01T00:00:00.000Z | 2           | curry         |
| C            | 2021-01-01T00:00:00.000Z | 3           | ramen         |
| A            | 2021-01-10T00:00:00.000Z | 3           | ramen         |
| B            | 2021-01-16T00:00:00.000Z | 3           | ramen         |
| A            | 2021-01-01T00:00:00.000Z | 1           | sushi         |
| B            | 2021-01-01T00:00:00.000Z | 2           | curry         |
###### The above code and table returns the first item purchased for every customer 
###### Ex: Customer B first item purchased was sushi 

## What is the most popular menu item and how many times was it purchased per customer?
```sql
SELECT sales.customer_id, 
sales.product_id, 
menu.product_name,
COUNT(menu.product_name) OVER (
PARTITION BY sales.customer_id) AS times_ordered
FROM dannys_diner.sales LEFT JOIN dannys_diner.menu 
ON sales.product_id = menu.product_id 
GROUP BY sales.customer_id, sales.product_id, menu.product_name 
ORDER BY times_ordered DESC; 
```
| customer\_id | product\_id | product\_name | times\_ordered |
| ------------ | ----------- | ------------- | -------------- |
| A            | 1           | sushi         | 3              |
| A            | 2           | curry         | 3              |
| A            | 3           | ramen         | 3              |
| B            | 1           | sushi         | 3              |
| B            | 2           | curry         | 3              |
| B            | 3           | ramen         | 3              |
| C            | 3           | ramen         | 1              |
###### The above code and table returns the most popular item on the menu as well as the number of times it was ordered

## Most popular menu item for each customer 
```sql
SELECT DISTINCT sales.customer_id, 
sales.product_id, 
menu.product_name, 
COUNT(menu.product_name) AS customer_favorites
FROM dannys_diner.sales LEFT JOIN dannys_diner.menu 
ON sales.product_id = menu.product_id
GROUP BY sales.customer_id, sales.product_id, menu.product_name
ORDER BY sales.customer_id DESC, customer_favorites DESC; 
```
| customer\_id | product\_id | product\_name | customer\_favorites |
| ------------ | ----------- | ------------- | ------------------- |
| C            | 3           | ramen         | 3                   |
| B            | 1           | sushi         | 2                   |
| B            | 2           | curry         | 2                   |
| B            | 3           | ramen         | 2                   |
| A            | 3           | ramen         | 3                   |
| A            | 2           | curry         | 2                   |
| A            | 1           | sushi         | 1                   |
###### The above code and table returns the most popular menu item per customer 
###### Ex: Customer C ordered ramen a total of 3 times making it their favorite menu item

## Which item was purchased first after they became a loyalty member?
```sql


