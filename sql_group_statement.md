-- GROUP BY and Aggregate functions
-- GROUP BY will allow us to aggregate data and apply functions to better understand how data is distributed per category.

-- Aggregation Functions
-- Main idea behind an aggregate function is to take multiple inputs and return a single output
-- https://www.postgresql.org/docs/9.5/functions-aggregate.html

-- Most Common Aggregate Functions - 
-- 1. AVG()   - Returns Average value
-- 2. COUNT() - Returns number of values
-- 3. MAX()   - Return maximum value
-- 4. MIN()   - Return minimum value
-- 5. SUM()   - Returns the sum of all values

-- Aggregate function calls happen only in the SELECT or HAVING clause.

-- AVG() - Returns floating point value which can have many decimal places (Eg 2.13857484)
-- We can use ROUND() to specify the precission after the decimal

-- COUNT() simply returns the number of rows, which simply means by convention we just use COUNT(*)

SELECT MAX(replacement_cost) FROM film;
-- SELECT * FROM film WHERE replacement_cost < MAX(replacement_cost) ORDER BY replacement_cost DESC LIMIT 1;


SELECT MIN(replacement_cost), MAX(replacement_cost), AVG(replacement_cost) FROM film;

-- AVG & ROUND Function
-- ROUND( Number, NoOfDecimanlPlaces )
SELECT ROUND(AVG(replacement_cost),2) FROM film;

SELECT SUM(replacement_cost) FROM film;


-- GROUP BY 
-- 

-- Kinds of Data
--                   
--                  
--        Categorical   - Nominal  - Named categories
--     /  (qualitative) - Ordinal - Categories with an implied order
--    /
-- Data 
--    \
--     \
--          Numerical    -  Discrete - Only particular numbers
--       (quantitative)  -  Continuous - Any numeric value

-- Example Statement
-- SELECT category_col, AGG(data_col) FROM table GROUP BY catrgory_col;

-- GROUP BY clause must appear right after a FROM or WHERE statement

-- Example Statement 2
-- SELECT category_col, AGG(data_col) FROM table  WHERE category_col != 'AnyValue' GROUP BY catrgory_col;

-- GROUP BY example
-- SELECT company,division, SUM(sales) FROM finance_table GROUP BY company,division

-- SELECT company,SUM(sales) FROM finance_table GROUP BY company ORDER BY SUM(sales) LIMIT 5;

SELECT * FROM payment;
SELECT customer_id, SUM(amount) FROM payment GROUP BY customer_id ORDER BY SUM(amount) DESC;

SELECT * FROM payment;
SELECT staff_id,customer_id, SUM(amount) FROM payment GROUP BY staff_id,customer_id ORDER BY staff_id,customer_id;

-- DATE() - To convert Timestamp to Date
SELECT * FROM payment;
SELECT DATE(payment_Date),SUM(amount) FROM payment GROUP BY DATE(payment_date) ORDER BY SUM(amount);


-- We have two staff members, with Staff IDs 1 and 2. We want to give a bonus
-- to the staff member that handled the most payments. How many payments did each
-- staff memeber handle and who gets the bonus?
SELECT * FROM payment;
SELECT staff_id,COUNT(DATE(payment_date)) FROM payment GROUP BY staff_id;
SELECT staff_id,COUNT(DATE(payment_date)) FROM payment GROUP BY staff_id ORDER BY COUNT(DATE(payment_date)) DESC LIMIT 1;

SELECT staff_id,COUNT(*) FROM payment GROUP BY staff_id;


-- Corporate HQ is conducting a study on the relationship between replacement cost and a moviee MPAA 
-- rating (eg G,PG,R etc). What is the avg replacement cost per MPAA rating?
SELECT rating, ROUND(AVG(replacement_cost),3) FROM film GROUP BY rating;


-- We are running a promotion to reward our top 5 customers with coupons.
-- What are the customer ids of the top 5 customers by total spend?
SELECT * FROM payment;
SELECT customer_id, SUM(amount) FROM payment GROUP BY customer_id ORDER BY SUM(amount) DESC LIMIT 5;


-- HAVING Clause
-- HAVING clause allows us to filter AFTER an aggregation has already taken place.

-- SELECT company, SUM(sales) FROM finance_table WHERE company != 'Google' GROUP BY company;
-- SELECT company, SUM(sales) FROM finance_table WHERE company != 'Google' GROUP BY company HAVING SUM(sales)>1000;

SELECT customer_id,SUM(amount) FROM payment GROUP BY customer_id HAVING SUM(amount)>100;

SELECT store_id,COUNT(customer_id) FROM customer GROUP BY store_id HAVING COUNT(customer_id) > 300;


-- We are launching a platinum service for our most loyal customers. We will assign
-- platinum status to customers that have had 40 or more transaction payments. What 
-- Customers(customer_ids) are eligible for platinum status?
SELECT customer_id, COUNT(payment_id) FROM payment GROUP BY customer_id HAVING COUNT(payment_id)>=40;

-- What are the customer_ids of customer who have spent more than $100 in payment transactions with out staff_id memeber 2?
SELECT customer_id, SUM(amount) FROM payment WHERE staff_id = 2 GROUP BY customer_id HAVING SUM(amount)>100;


-- Return the customer IDs of customers who have spent at least $110 with the staff member who has an ID of 2.
SELECT customer_id,SUM(amount) FROM payment WHERE staff_id=2 GROUP BY customer_id HAVING SUM(amount)>110;


-- How many films begin with the letter J?
SELECT COUNT(*) FROM film WHERE title LIKE 'J%';


-- What customer has the highest customer ID number whose name starts with an 'E' and has an address ID lower than 500?
SELECT first_name,last_name FROM customer WHERE first_name LIKE 'E%' AND address_id<500 ORDER BY customer_id DESC LIMIT 1;

