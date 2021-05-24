-- JOINs
-- 1. https://blog.codinghorror.com/a-visual-explanation-of-sql-joins/
-- 2. http://www.sql-join.com/
-- 3. https://en.wikipedia.org/wiki/Join_(SQL)

-- JOINs allow us to combine information from multiple tables

-- AS clause allows us to create an 'alias' for a column or result
-- Ex > Syntax - SELECT column AS new_name FROM table
--    > SELECT SUM(column) AS new_name FROM table
-- AS operator gets execyted at the very end of a query, we can't use ALIAS inside a WHERE operator.

SELECT COUNT(amount) AS No_Of_Txn FROM payment;
SELECT customer_id, SUM(amount) AS Total_Spent  FROM payment GROUP BY customer_id;


-- JOIN
-- INNER JOIN - will result with set of records that match in both tables
-- SYNTAX - 
-- SELECT * FROM TableA INNER JOIN TableB ON TableA.col_match = TableB.col_match
-- INNER JOIN is symmetrical can switch table order as its an intersection

-- SELECT * FROM Registrations INNER JOIN Logins ON Registrations.name = Logins.name
-- SELECT reg_id, Logins.name FROM Registrations INNER JOIN Logins ON Registrations.name = Logins.name

SELECT payment_id,payment.customer_id,staff_id,amount, first_name,last_name FROM payment INNER JOIN customer ON payment.customer_id = customer.customer_id;

-- FULL OUTER JOIN
-- Grabs everything from both Table
-- This JOIN is also symmetrical
-- SYNTAX
-- SELECT * FROM Registrations FULL OUTER JOIN Logins ON Registration.name = Logins.name
-- 'null' will be added for values present in other Table

-- To get Everything except data in common(intersection) of both Tables
-- SELECT * FROM TableA FULL OUTER JOIN TableB ON TableA.col_match = TableB.col_match
-- WHERE TableA.id IS null OR TableB.id IS null

SELECT COUNT(DISTINCT(customer.customer_id)) FROM customer FULL OUTER JOIN payment ON customer.customer_id = payment.customer_id WHERE customer.customer_id IS null OR payment.payment_id IS null;

-- LEFT OUTER JOIN
-- A LEFT OUTER JOIN results in the set of records that are in the left table, 
-- if there is no match with the right table, the results are null.
-- SYNTAX
-- SELECT * FROM TableA LEFT OUTER JOIN TableB ON TableA.col_match = TableB.col_match
-- SELECT * FROM Registrations LEFT OUTER JOIN Logins ON Registrations.name = Logins.name

-- If we want data unique to A and not found in B
-- SELECT * FROM Registrations LEFT OUTER JOIN Logins ON Registrations.name = Logins.name WHERE Logins.id IS null

SELECT * FROM film;
SELECT * FROM inventory;

SELECT film.film_id, inventory_id,store_id, title FROM film
LEFT JOIN inventory ON film.film_id = inventory.film_id WHERE inventory.film_id IS null;

-- RIGHT JOIN
-- Essentially same as LEFT JOIN, except the tables are switched.
-- This would be tje same as switching the table order in a LEFT OUTER JOIN

-- LEFT -> SELECT * FROM TableA LEFT OUTER JOIN TableB ON TableA.col_match = TableB.col_match
-- RIGHT -> SELECT * FROM TableA RIGHT OUTER JOIN TableB ON TableA.col_match = TableB.col_match

-- UNIONS
-- UNION operator is used to combine the result-set of two or more SELECT statements.
-- It basicall serves to directly concatenate two results together, essentially "pasting" them together.
-- SYNTAX
-- SELECT column_name(s) FROM table1  UNION  SELECT column_name(s) FROM table2;



-- California Sales tax laws have changed and we need to laert our customers to this through email.
-- What are the emails of the customers who live in California?
SELECT district,email FROM customer JOIN address ON customer.address_id = address.address_id WHERE district='California';


-- A customer walks in and is a guge fan of the actor 'Nick Wahlberg' and wants to know which movies he is in.
-- Get a list of all the movies "Nick Wahlberg" has been in.
SELECT title,first_name,last_name FROM actor INNER JOIN film_actor ON actor.actor_id = film_actor.actor_id 
INNER JOIN film ON film_actor.film_id=film.film_id WHERE first_name='Nick' AND last_name='Wahlberg';