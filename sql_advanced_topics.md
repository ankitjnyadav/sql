-- ADVANCED TOPICS
-- 1. Timestamps and EXTRACT
-- 2. Math Functions
-- 3. String Functions
-- 4. Sub-Query
-- 5. Self Join

-- DATE TIME DATATYPES
-- 1. TIME - Contains only time
-- 2. DATE - Contains only date
-- 3. TIMESTAMP - Contains date and time
-- 4. TIMESTAMPZ - Contains data, time and timezone

-- Functions & operations related to these specific data types:
-- 1. TIMEZONE
-- 2. NOW
-- 3. TIMEOFDAY
-- 4. CURRENT_TIME
-- 5. CURRENT_DATE

-- SHOW
-- SHOW will display the current setting of run-time parameters. 

SHOW ALL;
SHOW TIMEZONE;
SELECT NOW();
SELECT TIMEOFDAY();
SELECT CURRENT_TIME;
SELECT CURRENT_DATE;

-- EXTRACT()
-- Allows us to "extract" or obtain a sub-component of a date value
-- 1. YEAR
-- 2. MONTH
-- 3. DAY
-- 4. WEEK
-- 5. QUARTER
-- Usage -> EXTRACT(YEAR FROM date_column)

-- AGE()
-- Calculates and returns the current age given a timestamp
-- Usage -> AGE(date_column)

-- TO_CHAR()
-- General function to convert data types to text
-- Useful for timestamp formatting
-- Usage -> TO_CHAR(Date_column,'mm-dd-yyyy')

-- From Documention
-- to_char(timestamp, text)			text					convert time stamp to string			to_char(current_timestamp, 'HH12:MI:SS')
-- to_char(interval, text)			text					convert interval to string				to_char(interval '15h 2m 12s', 'HH24:MI:SS')
-- to_char(int, text)				text					convert integer to string				to_char(125, '999')
-- to_char(double precision, text)	text					convert real/double precision to string	to_char(125.8::real, '999D9')
-- to_char(numeric, text)			text					convert numeric to string				to_char(-125.8, '999D99S')
-- to_date(text, text)				date					convert string to date					to_date('05 Dec 2000', 'DD Mon YYYY')
-- to_number(text, text)			numeric					convert string to numeric				to_number('12,454.8-', '99G999D9S')
-- to_timestamp(text, text)		timestamp with time zone	convert string to time stamp			to_timestamp('05 Dec 2000', 'DD Mon YYYY')


SELECT EXTRACT(YEAR FROM payment_date) FROM payment;
SELECT EXTRACT(QUARTER FROM payment_date) FROM payment;
SELECT EXTRACT(MONTH FROM payment_date) FROM payment;
SELECT EXTRACT(DAY FROM payment_date) FROM payment;


SELECT AGE(payment_date) FROM payment;

-- TO_CHAR(expression, format)
SELECT TO_CHAR(payment_date,'MONTH-YYYY') FROM payment;
SELECT TO_CHAR(payment_date,'mon-YYYY') FROM payment;
SELECT TO_CHAR(payment_date,'MM-YYYY') FROM payment;


-- During which months did payments occur?
-- Format your ans to return back the full month name.
SELECT DISTINCT(EXTRACT(MONTH FROM payment_date)) AS MONTH FROM payment;
SELECT DISTINCT(TO_CHAR(payment_date,'MONTH')) AS MONTH FROM payment;


-- How many payments occurred on a Monday?
SELECT EXTRACT(DOW FROM payment_date) FROM payment;
SELECT COUNT(payment_date) FROM payment WHERE EXTRACT(DOW FROM payment_date)=1;


-- Mathematical Functions and Operators
-- https://www.postgresql.org/docs/9.5/functions-math.html
SELECT ROUND(rental_rate/replacement_cost,4)*100 AS PercentageCost FROM film;


-- String Functions and Operations
-- https://www.postgresql.org/docs/9.1/functions-string.html
SELECT LENGTH(first_name) FROM customer;
SELECT first_name||' '||last_name AS full_name FROM customer;

-- Generate Buisness Email
SELECT first_name||'.'||last_name||'@work.com' AS work_email FROM customer;
SELECT LEFT(first_name,1)||last_name||'@work.com' AS work_email FROM customer;
SELECT LOWER(LEFT(first_name,1)||last_name||'@work.com') AS work_email FROM customer;


-- SubQuery
-- A Sub Query allows us to contruct complex queries, essentially performing a query on the results of another query.
-- The syntax is straight forward and involves two SELECT statements.

-- How can we get a list of students who scored better than the average grade?
-- SELECT student,grade FROM test_scores WHERE grade > (SELECT AVG(grade) FROM test_scores);


-- SELECT student,grade FROM test_scores WHERE student IN (SELECT student FROM honor_roll_table);
-- SELECT student,grade FROM test_scores WHERE student IN (('Zack','Buckel','Tim'));

-- EXISTS 
-- The EXISTS operator is used to test for existence of rows in a subquery.
-- Typically a subquery is passed in the EXISTS() function to check if any rows are returned with the subquery.
-- Syntax -> SELECT column_name FROM table_name WHERE EXITS( SELECT column_name FROM table_name WHERE condition);

-- All rental rates greater than the avg rental rates.
SELECT * FROM film WHERE rental_rate > (SELECT AVG(rental_rate) FROM film);

-- Get film titles returned between certain dates.
SELECT title FROM film WHERE film_id IN (SELECT inventory.film_id FROM rental INNER JOIN inventory ON inventory.inventory_id = rental.inventory_id WHERE rental_date BETWEEN '2005-05-29' AND '2005-12-31') ORDER BY title;

-- Find customer who have atleast one payment whose amount is greater $11 and get their first & last name.
SELECT first_name,last_name FROM customer AS c WHERE EXISTS(SELECT * FROM payment AS p WHERE p.customer_id=c.customer_id AND amount > 11);


-- SELF JOIN
-- A self join is a query in which a table is joined to itself.
-- Self joins are useful for comparing values in a column of rows within the same table.
-- The self join can be viewed as a join of two copies of the same table.
-- The table is not actually copied, but SQL performs the command as though it were.
-- There is no special keyword for a self join, its simply standard JOIN syntax with the same table in both parts.
-- When using a self join it is necessary to use an alias for the table, otherwise the table names would be ambiguous.

-- Syntax -> SELECT tableA.col,tableB.col FROM table AS tableA JOIN table AS tableB ON tableA.some_col = tableB.other_col;
-- SELECT emp.name,report.name AS rep FROM employees AS emp JOIN employees AS report ON emp.emp_id = report.report_id;

-- What other film have same length
SELECT title,length FROM film WHERE length = 112;

SELECT f1.title,f2.title,f1.length FROM film AS f1 JOIN film AS f2 ON f1.film_id != f2.film_id AND f1.length = f2.length;



-- How can you retrieve all the information from the cd.facilities table?
SELECT * FROM cd.facilities;

-- We want to print out a list of all of the facilities and their cost to members. 
-- How would you retrieve a list of only facility names and costs?
SELECT name, membercost FROM cd.facilities;

-- How can you produce a list of facilities that charge a fee to members?
SELECT * FROM cd.facilities WHERE membercost > 0;

-- How can you produce a list of facilities that charge a fee to members, 
-- and that fee is less than 1/50th of the monthly maintenance cost? Return the facid, 
-- facility name, member cost, and monthly maintenance of the facilities in question.


-- SELECT facid, name, membercost, monthlymaintenance FROM cd.facilities WHERE monthlymaintenance > (SELECT monthlymaintenance/50 FROM cd.facilities);
SELECT facid, name, membercost, monthlymaintenance
 FROM cd.facilities
 WHERE membercost > 0 AND
 (membercost < monthlymaintenance/50.0);


-- How can you produce a list of all facilities with the word 'Tennis' in their name?
SELECT * FROM cd.facilities WHERE name LIKE '%Tennis%';

-- How can you retrieve the details of facilities with ID 1 and 5? Try to do it without using the OR operator.
SELECT * FROM cd.facilities WHERE facid IN (SELECT * FROM cd.facilities WHERE facid=1 OR facid=5);
SELECT * FROM cd.facilities WHERE facid IN (1,5);


-- How can you produce a list of members who joined after the start of September 2012? Return the memid, surname, firstname, and joindate of the members in question.
SELECT * FROM cd.members WHERE EXTRACT(MONTH FROM joindate)>=9 AND EXTRACT(YEAR FROM joindate)>=2012;
SELECT memid, surname, firstname, joindate FROM cd.members WHERE joindate >= '2012-09-01';


-- How can you produce an ordered list of the first 10 surnames in the members table? The list must not contain duplicates.
SELECT DISTINCT(surname) FROM cd.members ORDER BY surname LIMIT 10;


-- You'd like to get the signup date of your last member. How can you retrieve this information?
SELECT joindate FROM cd.members ORDER BY joindate DESC LIMIT 1;
SELECT MAX(joindate) AS latest_signup FROM cd.members;

-- Produce a count of the number of facilities that have a cost to guests of 10 or more.
SELECT COUNT(*) FROM cd.facilities WHERE guestcost>10;


-- Produce a list of the total number of slots booked per facility in the month of September 2012. Produce an output table consisting of facility id and slots, sorted by the number of slots.
SELECT name,SUM(slots) AS TotalSum FROM cd.bookings,cd.facilities WHERE cd.bookings.facid=cd.facilities.facid AND EXTRACT(MONTH FROM cd.bookings.starttime)=9 AND EXTRACT(YEAR FROM cd.bookings.starttime)=2012 GROUP BY name;


-- Produce a list of facilities with more than 1000 slots booked. Produce an output table consisting of facility id and total slots, sorted by facility id.
SELECT name,SUM(slots) AS TotalSum FROM cd.bookings,cd.facilities WHERE cd.bookings.facid=cd.facilities.facid GROUP BY name HAVING SUM(slots) > 1000 ;



-- How can you produce a list of the start times for bookings for tennis courts, for the date '2012-09-21'? Return a list of start time and facility name pairings, ordered by the time.
SELECT name,starttime FROM cd.bookings,cd.facilities WHERE cd.bookings.facid=cd.facilities.facid AND DATE(starttime)='2012-09-21' AND name LIKE 'Tennis%';


-- How can you produce a list of the start times for bookings by members named 'David Farrell'?
SELECT starttime FROM cd.bookings,cd.members WHERE cd.bookings.memid=cd.members.memid AND firstname='David' AND surname='Farrell'












































