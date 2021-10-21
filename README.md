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
| customer\_id | order\_date              | product\_id | product\_name | price | customer\_total |
| ------------ | ------------------------ | ----------- | ------------- | ----- | --------------- |
| B            | 2021-01-02T00:00:00.000Z | 2           | curry         | 15    | 74              |
| B            | 2021-01-04T00:00:00.000Z | 1           | sushi         | 10    | 74              |
| B            | 2021-01-11T00:00:00.000Z | 1           | sushi         | 10    | 74              |
| B            | 2021-01-16T00:00:00.000Z | 3           | ramen         | 12    | 74              |
| B            | 2021-02-01T00:00:00.000Z | 3           | ramen         | 12    | 74              |
| B            | 2021-01-01T00:00:00.000Z | 2           | curry         | 15    | 74              |
| A            | 2021-01-01T00:00:00.000Z | 1           | sushi         | 10    | 64              |
| A            | 2021-01-01T00:00:00.000Z | 2           | curry         | 15    | 64              |
| A            | 2021-01-07T00:00:00.000Z | 2           | curry         | 15    | 64              |
| A            | 2021-01-10T00:00:00.000Z | 3           | ramen         | 12    | 64              |
| A            | 2021-01-11T00:00:00.000Z | 3           | ramen         | 12    | 64              |
| C            | 2021-01-01T00:00:00.000Z | 3           | ramen         | 12    | 24              |
| C            | 2021-01-07T00:00:00.000Z | 3           | ramen         | 12    | 24              |
###### The above code and table returns the total amount of moeny spent between customers A, B and C
###### Ex: Customer A spent a total of $64 dollars and Customer C spent the least with a total of $24

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
| customer\_id | order\_date              | total\_customer\_days |
| ------------ | ------------------------ | --------------------- |
| B            | 2021-01-01T00:00:00.000Z | 6                     |
| B            | 2021-01-02T00:00:00.000Z | 6                     |
| B            | 2021-01-04T00:00:00.000Z | 6                     |
| B            | 2021-01-11T00:00:00.000Z | 6                     |
| B            | 2021-01-16T00:00:00.000Z | 6                     |
| B            | 2021-02-01T00:00:00.000Z | 6                     |
| A            | 2021-01-01T00:00:00.000Z | 4                     |
| A            | 2021-01-07T00:00:00.000Z | 4                     |
| A            | 2021-01-10T00:00:00.000Z | 4                     |
| A            | 2021-01-11T00:00:00.000Z | 4                     |
| C            | 2021-01-01T00:00:00.000Z | 2                     |
| C            | 2021-01-07T00:00:00.000Z | 2                     |
###### The above code and table returns the total amount of days each customer visited this business
###### Ex: Customer B visited this business the most days with a total of 6

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
| customer\_id | order\_date              | product\_name |
| ------------ | ------------------------ | ------------- |
| A            | 2021-01-01T00:00:00.000Z | curry         |
| A            | 2021-01-01T00:00:00.000Z | sushi         |
| B            | 2021-01-01T00:00:00.000Z | curry         |
| C            | 2021-01-01T00:00:00.000Z | ramen         |
| B            | 2021-01-02T00:00:00.000Z | curry         |
| B            | 2021-01-04T00:00:00.000Z | sushi         |
| A            | 2021-01-07T00:00:00.000Z | curry         |
| C            | 2021-01-07T00:00:00.000Z | ramen         |
| A            | 2021-01-10T00:00:00.000Z | ramen         |
| A            | 2021-01-11T00:00:00.000Z | ramen         |
| B            | 2021-01-11T00:00:00.000Z | sushi         |
| B            | 2021-01-16T00:00:00.000Z | ramen         |
| B            | 2021-02-01T00:00:00.000Z | ramen         |
###### The above code and table returns the first item bought by each customer
###### Ex: The first item bought by customer C was ramen

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
| customer\_id | product\_id | product\_name | times\_ordered\_bycustomer | most\_popular\_item |
| ------------ | ----------- | ------------- | -------------------------- | ------------------- |
| A            | 3           | ramen         | 3                          | 3                   |
| A            | 2           | curry         | 3                          | 2                   |
| A            | 1           | sushi         | 3                          | 2                   |
| B            | 2           | curry         | 3                          | 2                   |
| B            | 1           | sushi         | 3                          | 2                   |
| B            | 3           | ramen         | 3                          | 3                   |
| C            | 3           | ramen         | 1                          | 3                   |
###### The above code and table returns the most popular item on the menu as well as how many times each customer ordered it 
###### Ex: The most popular item on the menu  was ramen which was ordered 3 times by customers A and B 


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
| customer\_id | product\_id | product\_name | times\_ordered\_bycustomer |
| ------------ | ----------- | ------------- | -------------------------- |
| A            | 1           | sushi         | 3                          |
| A            | 2           | curry         | 3                          |
| A            | 3           | ramen         | 3                          |
| B            | 1           | sushi         | 3                          |
| B            | 2           | curry         | 3                          |
| B            | 3           | ramen         | 3                          |
| C            | 3           | ramen         | 1                          |
###### The above code and table returns the most popular items by customer 
###### Ex: The most popular item for customer C was ramen

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

## Customer will earn 2x points every week after they join the loyalty program for any items purchased
## This includes the customers join_date 
## How many points do customers have at the end of January?










