

```sql
create table customer_orders (
order_id integer,
customer_id integer,
order_date date,
order_amount integer
);

insert into customer_orders values(1,100,cast('2022-01-01' as date),2000),(2,200,cast('2022-01-01' as date),2500),(3,300,cast('2022-01-01' as date),2100)
,(4,100,cast('2022-01-02' as date),2000),(5,400,cast('2022-01-02' as date),2200),(6,500,cast('2022-01-02' as date),2700)
,(7,100,cast('2022-01-03' as date),3000),(8,400,cast('2022-01-03' as date),1000),(9,600,cast('2022-01-03' as date),3000);


select * from customer_orders;
```

<img width="576" height="171" alt="Screenshot 2025-11-17 213723" src="https://github.com/user-attachments/assets/45297a16-19e4-4eea-9b48-0c8392108f50" />

```sql
select * from customer_orders;
```
| order_id | customer_id | order_date | order_amount |
|----------|-------------|------------|--------------|
| 1        | 100         | 2022-01-01 | 2000         |
| 2        | 200         | 2022-01-01 | 2500         |
| 3        | 300         | 2022-01-01 | 2100         |
| 4        | 100         | 2022-01-02 | 2000         |
| 5        | 400         | 2022-01-02 | 2200         |
| 6        | 500         | 2022-01-02 | 2700         |
| 7        | 100         | 2022-01-03 | 3000         |
| 8        | 400         | 2022-01-03 | 1000         |
| 9        | 600         | 2022-01-03 | 3000         |


### How many New and Repeat Customers everyday

### Step 1
```sql
# Figure the output
```
| Date     |  New_Cust  | Repeat_Cust |
|----------|-------------|----------|
| 2022-01-01  |  3       |  0     |
| 2022-01-02  |  2       |  1     |
| 2022-01-03  |  1       |   2    |


### Step 2
```sql
# For each customer find their first order date

SELECT customer_id, MIN(order_date) AS first_visit_date
FROM customer_orders
GROUP BY customer_id ;
```

| customer_id | first_visit_date |
|-------------|-------------|
|         100 | 2022-01-01  |
|         200 | 2022-01-01  |
|         300 | 2022-01-01  |
|         400 | 2022-01-02  |
|         500 | 2022-01-02  |
|         600 | 2022-01-03  |



### Step 3
``` sql
# All customer details & Their first visit date

WITH first_visit AS
(
SELECT customer_id, MIN(order_date) AS first_visit_date
 FROM customer_orders
GROUP BY customer_id ) 

SELECT co.* , fv.first_visit_date FROM customer_orders AS co
INNER JOIN first_visit AS fv ON co.customer_id = fv.customer_id 
```

| order_id | customer_id | order_date | order_amount | first_visit_date |
|----------|-------------|------------|--------------|------------------|
|        1 |         100 | 2022-01-01 |         2000 | 2022-01-01       |
|        2 |         200 | 2022-01-01 |         2500 | 2022-01-01       |
|        3 |         300 | 2022-01-01 |         2100 | 2022-01-01       |
|        4 |         100 | 2022-01-02 |         2000 | 2022-01-01       |
|        5 |         400 | 2022-01-02 |         2200 | 2022-01-02       |
|        6 |         500 | 2022-01-02 |         2700 | 2022-01-02       |
|        7 |         100 | 2022-01-03 |         3000 | 2022-01-01       |
|        8 |         400 | 2022-01-03 |         1000 | 2022-01-02       |
|        9 |         600 | 2022-01-03 |         3000 | 2022-01-03       |

### Step 4
``` sql
# Creating 2 seperate flags with two CASE statements

WITH first_visit AS
(
SELECT customer_id, MIN(order_date) AS first_visit_date
 FROM customer_orders
GROUP BY customer_id ) 

SELECT co.* , fv.first_visit_date ,
CASE WHEN co.order_date = fv.first_visit_date THEN 1 ELSE 0 END as First_visit_flag ,
CASE WHEN co.order_date !=  fv.first_visit_date THEN 1 ELSE 0 END as Repeat_visit_flag

FROM customer_orders AS co
INNER JOIN first_visit AS fv ON co.customer_id = fv.customer_id 
```

| order_id | customer_id | order_date | order_amount | first_visit_date | First_visit_flag | Repeat_visit_flag |
|----------|-------------|------------|--------------|------------------|------------------|-------------------|
|        1 |         100 | 2022-01-01 |         2000 | 2022-01-01       |                1 |                 0 |
|        2 |         200 | 2022-01-01 |         2500 | 2022-01-01       |                1 |                 0 |
|        3 |         300 | 2022-01-01 |         2100 | 2022-01-01       |                1 |                 0 |
|        4 |         100 | 2022-01-02 |         2000 | 2022-01-01       |                0 |                 1 |
|        5 |         400 | 2022-01-02 |         2200 | 2022-01-02       |                1 |                 0 |
|        6 |         500 | 2022-01-02 |         2700 | 2022-01-02       |                1 |                 0 |
|        7 |         100 | 2022-01-03 |         3000 | 2022-01-01       |                0 |                 1 |
|        8 |         400 | 2022-01-03 |         1000 | 2022-01-02       |                0 |                 1 |
|        9 |         600 | 2022-01-03 |         3000 | 2022-01-03       |                1 |                 0 |


### Step 5
``` sql
# aggregate for the output

WITH first_visit AS
(
SELECT customer_id, MIN(order_date) AS first_visit_date
 FROM customer_orders
GROUP BY customer_id ) ,

Visit_flag AS
(
SELECT co.* , fv.first_visit_date ,
CASE WHEN co.order_date = fv.first_visit_date THEN 1 ELSE 0 END as First_visit_flag ,
CASE WHEN co.order_date !=  fv.first_visit_date THEN 1 ELSE 0 END as Repeat_visit_flag
FROM customer_orders AS co
INNER JOIN first_visit AS fv ON co.customer_id = fv.customer_id 
ORDER BY order_id )

SELECT order_date, 
SUM(first_visit_flag) AS Number_of_new_Cust,
SUM(Repeat_visit_flag) AS Number_of_Repeat_cust
FROM Visit_flag
GROUP BY order_date; 
```

| Order_Date     |  New_Cust  | Repeat_Cust |
|----------|-------------|----------|
| 2022-01-01  |  3       |  0     |
| 2022-01-02  |  2       |  1     |
| 2022-01-03  |  1       |   2    |




























