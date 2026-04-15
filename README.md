Brazilian E-Commerce: End-to-End SQL Data Engineering & Analytics

Project Overview

This project is a deep-dive into the Olist Brazilian E-Commerce dataset. I transitioned from raw, unstructured CSV files to a fully functional relational database in MySQL. This involved overcoming significant data ingestion hurdles, performing rigorous data cleaning, and executing complex SQL queries to extract actionable business insights regarding logistics, customer retention, and revenue growth.

Technical Stack
* Database: MySQL Workbench 8.0
* Language: SQL (Intermediate to Advanced)
* Techniques: CTEs, Window Functions, Data Ingestion, Relational Schema Design, Data Cleaning.
Challenges & "Building in Public" (The Hurdles)
Every project has its Plan A that fails. Here is how I navigated mine:
1. The Import Wizard Bottleneck
* The Issue: Initially, I used the MySQL Table Data Import Wizard. For 100k+ rows, the wizard was incredibly slow, causing frequent timeouts and freezing my system.
* The Solution: I pivoted to a programmatic approach using LOAD DATA LOCAL INFILE. This allowed me to bulk-load data in seconds rather than hours.
  
2. The Security & Permission Rabbit Hole
* The Error: I encountered Error 2068 and Error 3948 (Loading local data is disabled).
* The Fix: I had to modify the global variables (SET GLOBAL local_infile = 1;) and configure the Advanced Connection Settings in MySQL Workbench (OPT_LOCAL_INFILE=1). This taught me how to handle server-side security protocols—a vital skill for any Database Administrator.
  
3. Rusty to Ready
* The Reflection: Having spent time with other data tools, I initially felt "syntax rust" with SQL. However, by building this in public and tackling real-world errors, the logic of JOINS, CTEs, and Window Functions returned quickly. I am committed to mastering SQL one project at a time.
  
Database Schema

The project utilizes 3 core tables from the Olist dataset:

* customers: Unique identifiers, location, and city data.
* orders: Transactional timestamps (purchase, shipping, delivery).
* order_items: Pricing, freight (shipping) costs, and seller information.

Key Insights & SQL Deep Dives

1. Delivery Performance (Expectation vs. Reality)

I used DATEDIFF to compare estimated delivery dates with actual delivery dates.
* Insight: Found that "Late" deliveries drop customer review scores by an average of 1.4 points, directly impacting customer retention.
  
2. Customer Lifetime Value (CLV)
Using CTEs, I calculated the total revenue generated per unique customer throughout their history.

WITH customer_revenue AS (
    SELECT customer_unique_id, SUM(price) AS total_spent
    FROM customers c
    JOIN orders o ON c.customer_id = o.customer_id
    JOIN order_items oi ON o.order_id = oi.order_id
    GROUP BY customer_unique_id
)
SELECT AVG(total_spent) FROM customer_revenue;

3. Market Basket Analysis (Affinity)
I identified which product categories are frequently bought together in the same order_id to suggest potential bundling strategies for marketing.

5. Cumulative Revenue Growth
Utilized Window Functions to track the day-by-day running total of sales

SELECT 
    DATE(order_purchase_timestamp) AS order_date, 
    SUM(price) OVER(ORDER BY order_purchase_timestamp) AS running_total
FROM orders o JOIN order_items oi ON o.order_id = oi.order_id;

What I Learned

* Performance Optimization: Programmatic data loading is always superior to UI-based wizards for large datasets.
* Data Integrity: Standardizing city names (UPPER) and handling NULL values in delivery dates is essential for accurate logistics reporting.
* Resilience: Error codes are not roadblocks; they are the database's way of teaching you configuration and security.
  
How to Run
1. Clone this repository.
2. Open MySQL Workbench and create a schema named olist_project.
3. Ensure local_infile is enabled in your connection settings.
4. Run the provided scripts in the /scripts folder
   
Contact & Connect
I’m currently building my data portfolio in public. If you have any questions about my process or want to connect, find me on LinkedIn or Medium!

http://linkedin.com/in/ife-okoli
