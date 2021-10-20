# Dannys_Diner
![Unknown](https://user-images.githubusercontent.com/77989300/137805636-e0786535-6b1e-4f81-97fb-99b44894b527.jpeg)

## Goal Of Analysis

###### Strengthen the relationship between small-business owner and their target audience 
###### Extract and return valuable insights into customer spending behavior 
###### Answer business question for small-business owner 

## Total amount spent by each customer?
```sql
SELECT sales.customer_id, 
sales.order_date, 
sales.product_id, 
menu.product_name,
menu.price, 
SUM(menu.price) OVER (
PARTITION BY sales.customer_id) AS customer_total
FROM dannys_diner.sales
LEFT JOIN dannys_diner.menu
ON sales.product_id = menu.product_id 
GROUP BY sales.customer_id, sales.order_date, sales.product_id, menu.product_name, menu.price
ORDER BY customer_total DESC;
```

## How many total days has each customer visited this business? 
```sql
SELECT DISTINCT sales.customer_id,
       sales.order_date, 
       COUNT(sales.order_date) OVER (
       PARTITION BY customer_id) AS total_customer_days
FROM dannys_diner.sales
GROUP BY sales.customer_id, sales.order_date
ORDER BY total_customer_days DESC; 
```

## What was the first item purchased by each customer
```sql
SELECT sales.customer_id, 
sales.order_date,
menu.product_name
FROM dannys_diner.sales
LEFT JOIN dannys_diner.menu
ON sales.product_id = menu.product_id 
GROUP BY sales.customer_id, 
        sales.order_date,
        menu.product_name
ORDER BY sales.order_date ASC, sales.customer_id ASC; 
```

## What is the most popular menu item and how many times was it purchased per customer?
```sql
SELECT sales.customer_id, 
      sales.product_id,
      menu.product_name,
      COUNT(menu.product_name) OVER (
      PARTITION BY customer_id) AS times_ordered_bycustomer,
      COUNT(menu.product_name) OVER (
      PARTITION BY product_name) AS most_popular_item
FROM dannys_diner.sales
LEFT JOIN dannys_diner.menu
ON sales.product_id = menu.product_id 
GROUP BY sales.customer_id, 
        sales.product_id, 
        menu.product_name
ORDER BY sales.customer_id ASC;
```

## Most popular menu item for each customer 
```sql
SELECT sales.customer_id, 
      sales.product_id,
      menu.product_name,
      COUNT(menu.product_name) OVER (
      PARTITION BY customer_id) AS times_ordered_bycustomer
FROM dannys_diner.sales
LEFT JOIN dannys_diner.menu
ON sales.product_id = menu.product_id 
GROUP BY sales.customer_id, 
        sales.product_id, 
        menu.product_name
ORDER BY sales.customer_id ASC; 
```

## Which item was purchased first after they became a loyalty member?
```sql
SELECT sales.customer_id, 
members.join_date,
sales.product_id,
menu.product_name
FROM dannys_diner.sales 
LEFT JOIN dannys_diner.members
ON sales.customer_id = members.customer_id
LEFT JOIN dannys_diner.menu 
ON sales.product_id = menu.product_id 
GROUP BY sales.customer_id, 
sales.product_id, 
members.join_date,
menu.product_name
ORDER BY members.join_date; 
```

## Which items were purchased before each customer became a loyalty member?
```sql
SELECT sales.customer_id, sales.order_date, sales.product_id, menu.product_name 
FROM dannys_diner.sales LEFT JOIN dannys_diner.menu 
ON sales.product_id = menu.product_id
WHERE order_date < '2021-01-09' AND order_date < '2021-01-07' 
GROUP BY sales.customer_id, sales.order_date, sales.product_id, menu.product_name  
ORDER BY sales.order_date DESC, sales.customer_id DESC;
```
| customer\_id | order\_date              | product\_id | product\_name |
| ------------ | ------------------------ | ----------- | ------------- |
| B            | 2021-01-04T00:00:00.000Z | 1           | sushi         |
| B            | 2021-01-02T00:00:00.000Z | 2           | curry         |
| C            | 2021-01-01T00:00:00.000Z | 3           | ramen         |
| B            | 2021-01-01T00:00:00.000Z | 2           | curry         |
| A            | 2021-01-01T00:00:00.000Z | 1           | sushi         |
| A            | 2021-01-01T00:00:00.000Z | 2           | curry         |
###### The above code and table returns the items purchased before customers A and B became loyalty members 
###### Ex: Customer B joined the loyalty program on '2021-01-09' and the item they purchased beforehand was sushi 
###### 2 Ex: Customer A joined the loyalty program on '2021-01-07' and they purchased sushi and curry before they became members

## Before each loyalty member signed up for the program, how many items did they buy and how much did they spend? 
```sql
SELECT sales.customer_id, 
      sales.order_date, 
      sales.product_id,
      menu.product_name, 
      menu.price,
      COUNT(menu.product_name) OVER (
      PARTITION BY customer_id) AS total_customer_items,
      SUM(menu.price) OVER (
      PARTITION BY customer_id) AS customer_spending_total
FROM dannys_diner.sales 
LEFT JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
WHERE sales.order_date < '2021-01-09' AND order_date < '2021-01-07'
GROUP BY sales.customer_id, 
        sales.order_date, 
        sales.product_id, 
        menu.product_name, 
        menu.price
ORDER BY sales.order_date DESC;
```










