
# Online Bookstore Analytics Database System
## Business Context

Online bookstores handle daily purchases from many customers ordering different books across various categories. As the number of orders increases, it becomes difficult to manually track which books sell the most, which customers purchase frequently, and how sales change over time.

To support better decision-making, the bookstore requires a structured database system to store customer information, book details, and purchase transactions in an organized and relational format.

## Data Challenge

The bookstore collects large amounts of transactional data, but raw data alone does not provide useful insights. The main challenges include:

Identifying customers who have never placed an order

Detecting books that have low or zero sales

Monitoring daily and cumulative sales trends

Ranking top customers and best-selling books

Comparing purchase behavior across time periods

Without proper SQL JOINs and analytical functions, extracting these insights would be slow and inefficient.

## Expected Outcome

The system is expected to:

Store customers, books, and orders using related tables with primary and foreign keys

Retrieve meaningful information using INNER, LEFT, RIGHT/FULL, and SELF JOINs

Perform advanced analysis using window functions such as ranking, running totals, and customer segmentation

Provide business insights that help improve marketing, stock management, and sales performance

By the end of the project, the database should simulate a real-world online bookstore analytics system that supports data-driven business decisions.
