
-- Data Types
-- Boolean - True or False
-- Character - char, varchar and text
-- Numeric - integer and floating-point number
-- Temporal - date, time, timestamp and interval
-- Not so common are -
-- UUID - Universally Unique Identifiers
-- Array - Stores an array of strings, numbers etc
-- JSON
-- Hstore - Key-value pair
-- Special types such as network address and geometric data
-- https://www.postgresql.org/docs/current/datatype.html

-- Primary Key
-- Is a coulmn or a group of columns used to identify a row uniquely in a table.
-- For eg: in dvdrental DB we saw customers had a unique, non-null customer_id column as their primary key.
-- Primary key are also important since they allow us to easily discen what columns should be used for
-- joining tables together.

-- Foreign Key
-- Is a field or group of fields in a table that uniquely identifies a row in another table.
-- A foreign key is defined in a table that references to the primary key of the other table.
-- The table that contains the foreign key is called referencing table or child table.
-- The table to which the foreign key references is called referenced table or parent table.
-- A table can have multiple foreign keys depending on its relationships with other tables.
-- For eg: In the dvdrental DB payment table, each payment row had its unique payment_id (primary key)
-- and identified the customer that made the payment through the customer_id (foreign key since it references 
-- the customer table's primary key)


-- Constraints
-- Are the rules enforced on data columns on table.
-- These are used to prevent invalid data from being entered into the database.
-- This ensures the accuracy and reliability of the data in the database.
-- Constraints can be divide into two main categories:
-- 1. Column Constraints - Constraints the data in a column to adhere to certain conditions.
-- 2. Table Constraints - Applied to the entire table rather than to an individual column.
-- The most common constraints used:
-- 1. NOT NULL - Ensures that column cannot have NULL value.
-- 2. UNIQUE - Ensures that all values in a column are different.
-- 3. PRIMARY KEY - Uniquely indetifies each row/recod in a database table.
-- 4. FOREIGN KEY - Constrains data based on columns in other tables.
-- 5. CHECK - Ensures that all values ina column satisfy certain conditions.
-- 6. EXCLUSION - Ensures that if any two rows are compared on the specified column
-- 	              or expression using the specified operator, not all of these comparisons 
-- 	              will return TRUE.


-- Table Constraints
-- 1. CHECK (condition) - to check a condition when inserting or updating data.
-- 2. REFERENCES - to constrain the value stored in the column that must exist in a column in another table.
-- 3. UNIQUE (column_list) - forces the values stored in the columns listed inside the parentheses to be unique.
-- 4. PRIMARY KEY (column_list) - Allows us to define the primary key that consists of multipe columns.


-- CREATE
-- Full General Syntax
-- CREATE TABLE table_name (column_name TYPE column constraint, column_name2 TYPE column_constraint, 
-- table_constraint table_constraint) INHERITS existing_table_name;

CREATE TABLE account(
	user_id SERIAL PRIMARY KEY,
	username VARCHAR(50) UNIQUE NOT NULL,
	password VARCHAR(50) NOT NULL,
	email VARCHAR(250) UNIQUE NOT NULL,
	created_on TIMESTAMP NOT NULL,
	last_login TIMESTAMP
);


CREATE TABLE job(
	job_id SERIAL PRIMARY KEY,
	job_name VARCHAR(200) UNIQUE NOT NULL
);

CREATE TABLE account_job(
	user_id INTEGER REFERENCES account(user_id),
	job_id INTEGER REFERENCES job(job_id),
	hire_Date TIMESTAMP
);


-- INSERT
-- Allows us to add in rows to a table.
-- General Syntax - INSERT INTO table (col1,col2) VALUES (val1,val2)
-- Syntax for INSERTING values from another table -
-- INSERT INTO table (col1,col2) SELECT col1,col2 FROM another_table WHERE condition;
-- Row values must match up for the table, including constraints.
-- SERIAL columns don't need values to be provided.

INSERT INTO account(username,password,email,created_on) 
VALUES ('AY','secret_password','AY@email.com',CURRENT_TIMESTAMP);

INSERT INTO job(job_name) VALUES ('Scientist');
INSERT INTO job(job_name) VALUES ('President');
SELECT * FROM job

INSERT INTO account_job(user_id,job_id,hire_date)
VALUES (1,1,CURRENT_TIMESTAMP)



-- UPDATE
-- General Syntax - UPDATE table SET col1 = val1, col2 = val2 WHERE condition;
-- Eg: UPDATE account SET last_login = CURRENT_TIMESTAMP WHERE last_login IS NULL;
-- Reset everything without WHERE condition
-- UPDATE account SET last_login = CURRENT_TIMESTAMP

-- Set based on another column
-- UPDATE account SET last_login = created_on
-- Using another table's values (UPDATE join)
-- UPDATE TableA SET original_col = TableB.new_col FROM TableB WHERE TableA.id = TableB.id;
-- Return affected rows
-- UPDATE account SET last_login = created_on RETURNING account_id, last_login;

SELECT * FROM account;
UPDATE account SET last_login = CURRENT_TIMESTAMP RETURNING *;
UPDATE account SET last_login = created_on RETURNING *;

SELECT * FROM account_job;
UPDATE account_job SET  hire_date = account.created_on 
FROM account WHERE account_job.user_id=account.user_id RETURNING *;



-- DELETE
-- To remove rows from a table.
-- For example: DELETE FROM tableA WHERE row_id = 1
-- For example: DELETE FROM tableA USING tableB WHERE tableA.id = tableB.id
-- To delete all rows froma table - DELETE FROM table_name
-- To see whats removed can add RETURNING call to return rows that were removed.


SELECT * FROM job;
DELETE FROM job WHERE job_name = 'Techie' RETURNING *;



-- ALTER Table
-- ALTER clause allows for changes to an existing table structure, such as:
-- 1. Adding, dropping or renaming columns
-- 2. Changing a column's data type
-- 3. Set DEFAULT values for a column
-- 4. Add CHECK constraints
-- 5. Renamce table
-- General Syntax
-- ALTER TABLE table_name action
-- Eg: ALTER TABLE table_name ADD COLUMN new_col TYPE
-- ALTER TABLE table_name DROP COLUMN col_name
-- ALTER TABLE table_name ALTER COLUMN col_name SET DEFAULT value
--												DROP DEFAULT 
--												SET NOT NULL
--												DROP NOT NULL
--												ADD CONSTRAINT constraint_name

CREATE TABLE info( info_id SERIAL PRIMARY KEY,
				 title VARCHAR(500) NOT NULL,
				 person VARCHAR(50) NOT NULL UNIQUE)

ALTER TABLE info RENAME TO info_new;

ALTER TABLE info_new RENAME COLUMN person TO people;
ALTER TABLE info_new ALTER COLUMN people DROP NOT NULL;
INSERT INTO info_new (title) VALUES ('Some TItle') RETURNING *;



-- DROP
-- DROP allows for the complete removal of a column in a Table

ALTER TABLE info_new DROP COLUMN IF EXISTS people;



-- CHECK Constraint
-- General Syntax
-- CREATE TABLE example( ex_id SERIAL PRIMARY KEY,
-- 						age SMALLINT CHECK (age>21),
-- 						parent_age SMALLINT CHECK (parent_age > age));



-- CHECK Constraint
-- General Syntax
-- CREATE TABLE example( ex_id SERIAL PRIMARY KEY,
-- 						age SMALLINT CHECK (age>21),
-- 						parent_age SMALLINT CHECK (parent_age > age));


CREATE TABLE emp(emp_id SERIAL PRIMARY KEY,
				firstname VARCHAR(50) NOT NULL,
				lastname VARCHAR(50) NOT NULL,
				bday DATE CHECK (bday > '1900-01-01'),
				hire_date DATE CHECK (hire_date > bday ),
				salary INTEGER CHECK (salary > 0));


INSERT INTO emp (firstname,lastname,bday,hire_date,salary)
VALUES
('Ankit','Yadav','1989-11-03','2010-01-01','25')