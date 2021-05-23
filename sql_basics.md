-- To get the distinct values we use DISTICNT
-- select * from film_category;
-- SELECT DISTINCT first_name,last_name from film;

select DISTINCT(rating) from film;
-- WHERE

select email from customer where first_name='Nancy' and last_name='Thomas';

select description from film where title='Outlaw Hanky';

select phone from address where address='259 Ipoh Drive';


--ORDER BY - ASCENDING or DESCENDING
-- ASC / DESC can be added to each column 
-- FILTERING first and then ORDERING in the end
-- ASC is by default
-- ORDER By can be used on multiple columns
-- ORDER First Coulmn and then Order by the 2nd column

SELECT * FROM customer ORDER BY first_name,last_name;
SELECT * FROM customer ORDER BY store_id DESC,first_name;
SELECT * FROM customer ORDER BY store_id DESC,first_name DESC;

-- LIMIT Statement
-- LIMIT is at the very end of a query request and is the last command to be executed
-- SELECT, Filter, Order and then we would limit our results


SELECT * FROM payment ORDER BY payment_date DESC;

-- What are the 10 most recent purchases?
SELECT * FROM payment ORDER BY payment_date DESC LIMIT 10;


-- What are the 10 most recent non 0 purchases?
SELECT * FROM payment WHERE amount > 0 ORDER BY payment_date DESC LIMIT 10;


-- We want to reward our first 10 paying customers.
-- What are the customer ids of the first 10 customers who created a payment?
SELECT customer_id FROM payment ORDER BY payment_date ASC LIMIT 10;

-- A customer wants to quickly rent a video to watch over their short lunch break
-- What are the titles of the 5 shortes in runtime movies?
SELECT title FROM film ORDER BY length ASC LIMIT 5;



-- COUNT function
-- on specific colum or all column like COLUMN (*)
-- SELECT COUNT(name) from students;

-- How many movies can the Customer can watch movies having 50mins or less runtime?
SELECT COUNT(*) FROM film WHERE length <= 50;


-- HOW Many unique names are present in Students;
SELECT first_name from customer;
SELECT DISTINCT first_name from customer;
SELECT COUNT(DISTINCT first_name) from customer;

-- BETWEEN operator
-- Where is the values between HIGH and LOW
-- value >= low AND value <= high
-- value BETWEEN low and high

-- NOT BETWEEN
-- value < low OR value > high
-- value NOT BETWEEN low AND high

-- BETWEEN can be used with dates
-- ISO 8601 standard - YYYY-MM-DD
-- date BETWEN '2007-01-31' AND '2007-12-31'

SELECT * FROM payment WHERE amount BETWEEN 8 AND 9;

SELECT * FROM payment WHERE payment_date BETWEEN '2007-02-01' AND '2007-02-15';


-- IN operator
-- Used to check values present in list of multiple options
-- value IN (option1, options2, option3)
-- SELECT color FROM table WHERE color IN ('red','blue','green')
-- SELECT color FROM table WHERE color NOT IN ('red','blue','green')

SELECT * FROM paymenT LIMIT 2;
SELECT * FROM payment WHERE amount IN (0.99,1.99);
SELECT count(*) FROM payment WHERE amount IN (0.99,1.99);
SELECT count(*) FROM payment WHERE amount NOT IN (0.99,1.99);


SELECT * FROM customer WHERE first_name IN ('John', 'Jake');


-- Pattern Matching
-- Like Operator - allows us to perform pattern matching against string data
-- Using Wildcard Percentage '%' - Any sequence/number of char
-- Using Wildcard Underscore '_' - Matches a single character

-- All names that begin with an 'A'
--  WHERE name LIKE 'A%'

-- All names that end with an 'a'
--  WHERE name LIKE '%a'

-- LIKE is case sensitive
-- ILIKE is case insensitive

-- Get all the Mission Impossible films
-- WHERE name LIKE 'Mission Impossible _'

-- Complex Example
-- WHERE name LIKE '_her%'
-- Cheryl
-- Theresa
-- Sherri

-- We can use RegEx 
-- https://www.geeksforgeeks.org/mysql-regular-expressions-regexp/
-- https://dataschool.com/how-to-teach-people-sql/how-regex-works-in-sql/


SELECT * FROM customer WHERE first_name LIKE '_her%';
SELECT * FROM customer WHERE first_name NOT LIKE '_her%';


-- How many payment transactions were greater than $5.00 ?
SELECT COUNT(*) FROM payment WHERE amount > 5.00;

-- How many actors have a first name that starts with the letter P?
SELECT COUNT(*) FROM actor WHERE first_name LIKE 'P%';

-- How many unique districts are our customers from ?
SELECT COUNT(DISTINCT(district)) FROM address;

-- Retrieve the list of names for those distinct districts from the previous question.
SELECT DISTINCT(district) FROM address;

-- How many films have a rating of R and a replacement cost between $5 and $15?
SELECT COUNT(*) FROM film WHERE rating='R' AND replacement_cost BETWEEN 5 and 15;

-- How many films have the word Truman somewhere in the title?
SELECT COUNT(*) FROM film WHERE title LIKE '%Truman%';








