
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

### Customer table

customers(customer_id, name, email, city, join_date)

CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100),
    city VARCHAR(50),
    join_date DATE
);

 INSERT INTO customers (customer_id, name, email, city, join_date) VALUES
(1, 'Alice Mukamana', 'alice@gmail.com', 'Kigali', '2025-01-10'),
(2, 'Brian Uwase', 'brian@gmail.com', 'Huye', '2025-01-15'),
(3, 'Claire Aline', 'claire@gmail.com', 'Musanze', '2025-02-01'),
(4, 'David Imani', 'david@gmail.com', 'Kigali', '2025-02-10'),
(5, 'Esther Keza', 'esther@gmail.com', 'Rubavu', '2025-02-20'),
(6, 'Frank Bosco', 'frank@gmail.com', 'Kigali', '2025-03-01'); -- no orders (for LEFT JOIN test)
books

### Order table

orders(order_id, customer_id, book_id, quantity, order_date, total_amount)



INSERT INTO orders (order_id, customer_id, book_id, quantity, order_date, total_amount) VALUES
(1, 1, 101, 2, '2025-03-01', 30.00),
(2, 2, 103, 1, '2025-03-01', 10.00),
(3, 1, 104, 1, '2025-03-02', 18.00),
(4, 3, 102, 3, '2025-03-02', 37.50),
(5, 4, 101, 1, '2025-03-03', 15.00),
(6, 2, 105, 2, '2025-03-03', 28.00),
(7, 5, 103, 4, '2025-03-04', 40.00),
(8, 3, 104, 1, '2025-03-05', 18.00),
(9, 1, 105, 1, '2025-03-06', 14.00);

### Book table

books(book_id, title, author, category, price, stock_quantity)

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    book_id INT,
    quantity INT,
    order_date DATE,
    total_amount DECIMAL(10,2),

    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (book_id) REFERENCES books(book_id)
);
CREATE TABLE books (
    book_id INT PRIMARY KEY,
    title VARCHAR(150) NOT NULL,
    author VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10,2),
    stock_quantity INT
);

INSERT INTO books (book_id, title, author, category, price, stock_quantity) VALUES
(101, 'Atomic Habits', 'James Clear', 'Self-Help', 15.00, 50),
(102, 'Think and Grow Rich', 'Napoleon Hill', 'Business', 12.50, 40),
(103, 'The Alchemist', 'Paulo Coelho', 'Fiction', 10.00, 60),
(104, 'Deep Work', 'Cal Newport', 'Productivity', 18.00, 30),
(105, 'Rich Dad Poor Dad', 'Robert Kiyosaki', 'Finance', 14.00, 35),
(106, 'Invisible Book', 'Unknown', 'Mystery', 20.00, 25); -- never sold


[](https://github.com/Ikireziannick/plsql_window_functions_-28453-_-Ikirezi-/blob/main/BOOK.jpeg?raw=true)
## INNER JOIN

### SQL CODE

SELECT c.name AS customer_name,
       b.title AS book_title,
       o.quantity,
       o.order_date
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id
INNER JOIN books b ON o.book_id = b.book_id;

### Interpretation

This query displays all completed purchases by matching customers with the books they bought. Only records that exist in all tables are shown.

## LEFT JOIN

### SQL CODE 

SELECT c.customer_id, c.name
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;

### Interpretation 

This shows customers who have not placed any orders. It helps the bookstore identify inactive customers for promotions or marketing.

## RIGHT JOIN

### SQL CODE

SELECT b.book_id, b.title
FROM orders o
RIGHT JOIN books b ON o.book_id = b.book_id
WHERE o.order_id IS NULL;

### Interpretation

This lists books that have never been purchased. It helps detect slow-moving or unpopular inventory.
(If your database doesn’t support RIGHT JOIN, swap tables and use LEFT JOIN.)

## FULL OUTER JOIN

### SQL CODE

SELECT c.name, o.order_id
FROM customers c
FULL OUTER JOIN orders o
ON c.customer_id = o.customer_id;

### Interpretation

This shows all customers and all orders, even when there is no match. It helps check for missing or unmatched records.
(If FULL JOIN isn’t supported, combine LEFT + RIGHT with UNION.)

## SELF JOIN

### SQL CODE

SELECT a.name AS customer1,
       b.name AS customer2,
       a.city
FROM customers a
JOIN customers b
ON a.city = b.city
AND a.customer_id < b.customer_id;

### Interpretation

This compares customers within the same city. It helps analyze regional customer groups.


# Window Functions Implememntation

## 1. Ranking Function (RANK)

 Rank customers by total money spent
 ### Query
 
 SELECT 
    c.customer_id,
    c.name,
    SUM(o.total_amount) AS total_spent,
    RANK() OVER (ORDER BY SUM(o.total_amount) DESC) AS spending_rank
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name;

### Interpretation
This query calculates how much each customer spent and ranks them from highest to lowest spender. It helps identify the bookstore’s top customers.

## 2. Aggregate Window (Running Total)

Track cumulative sales over time

### Query

SELECT 
    order_date,
    SUM(total_amount) AS daily_sales,
    SUM(SUM(total_amount)) 
        OVER (ORDER BY order_date) AS running_total
FROM orders
GROUP BY order_date;

### Interpretation
This shows daily sales and the cumulative total sales over time. It helps the bookstore monitor growth and overall performance.

## 3. Navigation Function (LAG)

Compare today’s sales with yesterday’s

### Query

SELECT 
    order_date,
    SUM(total_amount) AS daily_sales,
    LAG(SUM(total_amount)) 
        OVER (ORDER BY order_date) AS previous_day_sales
FROM orders
GROUP BY order_date;

### Interpretation
This compares each day’s sales with the previous day. It helps detect increases or decreases in sales trends.

## 4. Distribution Function (NTILE)

Divide customers into spending groups

### Query
SELECT 
    customer_id,
    SUM(total_amount) AS total_spent,
    NTILE(4) OVER (ORDER BY SUM(total_amount)) AS spending_group
FROM orders
GROUP BY customer_id;

### Interpretation
This divides customers into four groups based on spending levels. It helps segment customers into low, medium, and high spenders for targeted marketing.

# REFERENCES
Oracle Base - Analytic Functions (Window Functions) https://oracle-base.com/articles/misc/analytic-functions

Oracle Tutorial - Window Functions https://www.oracletutorial.com/oracle-analytic-functions/

W3Schools - SQL Window Functions https://www.w3schools.com/sql/sql_window_functions.asp

GeeksforGeeks - Window Functions in SQL https://www.geeksforgeeks.org/window-functions-in-sql/

Oracle PL/SQL Programming Book by Steven Feuerstein https://www.oreilly.com/library/view/oracle-plsql-programming/9781497668005/

 W3Schools - https://www.w3schools.com/sql/sql_join.asp

 youtube tutorial :https://youtu.be/G3lJAxg1cy8?si=FX5wG7xi0V6-M9VM



