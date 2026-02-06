
# Online Bookstore Analytics Database System
## Business Context

Online bookstores handle daily purchases from many customers ordering different books across various categories. As the number of orders increases, it becomes difficult to manually track which books sell the most, which customers purchase frequently, and how sales change over time.

To support better decision-making, the bookstore requires a structured database system to store customer information, book details, and purchase transactions in an organized and relational format.

## Data Challenge

The bookstore collects large amounts of transactional data, but raw data alone does not provide useful insights. The main challenges include:
Identifying customers who have never placed an order,Detecting books that have low or zero sales,Monitoring daily and cumulative sales trends,Ranking top customers and best-selling books,Comparing purchase behavior across time periods

Without proper SQL JOINs and analytical functions, extracting these insights would be slow and inefficient.

## Expected Outcome

The system is expected to:

Store customers, books, and orders using related tables with primary and foreign keys,Retrieve meaningful information using INNER, LEFT, RIGHT/FULL, and SELF JOINs,Perform advanced analysis using window functions such as ranking, running totals, and customer segmentation,Provide business insights that help improve marketing, stock management, and sales performance.
By the end of the project, the database should simulate a real-world online bookstore analytics system that supports data-driven business decisions.

# ER DIAGRAM
# SQL JOINS IMPLEMENTATION

We’ll use only 3 tables:

customers
books
orders

customers(customer_id, name, email, city, join_date)
books(book_id, title, author, category, price, stock_quantity)
orders(order_id, customer_id, book_id, quantity, order_date, total_amount)

# INNER JOIN

### SQL CODE

SELECT c.name AS customer_name,
       b.title AS book_title,
       o.quantity,
       o.order_date
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id
INNER JOIN books b ON o.book_id = b.book_id;

## INTERPRETATION

This query displays all completed purchases by matching customers with the books they bought. Only records that exist in all tables are shown.

# LEFT JOIN

## SQL CODE 

SELECT c.customer_id, c.name
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;

## INTERPRETATION 

This shows customers who have not placed any orders. It helps the bookstore identify inactive customers for promotions or marketing.

# RIGHT JOIN

## SQL CODE

SELECT b.book_id, b.title
FROM orders o
RIGHT JOIN books b ON o.book_id = b.book_id
WHERE o.order_id IS NULL;

## INTERPRETATION

This lists books that have never been purchased. It helps detect slow-moving or unpopular inventory.
(If your database doesn’t support RIGHT JOIN, swap tables and use LEFT JOIN.)

# FULL OUTER JOIN
## SQL CODE

SELECT c.name, o.order_id
FROM customers c
FULL OUTER JOIN orders o
ON c.customer_id = o.customer_id;

## INTERPRETATION

This shows all customers and all orders, even when there is no match. It helps check for missing or unmatched records.
(If FULL JOIN isn’t supported, combine LEFT + RIGHT with UNION.)

# SELF JOIN
## SQL CODE

SELECT a.name AS customer1,
       b.name AS customer2,
       a.city
FROM customers a
JOIN customers b
ON a.city = b.city
AND a.customer_id < b.customer_id;

## INTERPRETATION

This compares customers within the same city. It helps analyze regional customer groups.


# Window Functions Implememntation



