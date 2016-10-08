Name five important data types in SQL.

	nvarchar, int, datetime, varchar, text
What is the difference between View and Stored Procedure?

	View is used for retrieving and displaying data (SELECT). Stored procedures takes parameters, has control structures (i.e. IF... ELSE) and both DML and DDL statements.
What is DDL short for?

	Data Definition Language
What is DML short for?

	Data Modification Language
Which SQL statements does DDL consist of?

	CREATE, ALTER, DELETE
Which SQL statements does DML consist of?

	SELECT, UPDATE, INSERT, DELETE
Which keyword is used for changing or updating a procedure?

	ALTER
How can you spot a variable in a procedure?

	DECLARE for declaration and @ as prefix.
How do you ”run” a Stored Procedure?

	EXEC or EXECUTE <procedurename>

How will you retrieve a column value (i.e. Blogtitle) in a table of the row that has been deleted (i.e. in a trigger)?

	SELECT Blogtitle FROM inserted
Which kind of a key exists on the many-side in a relationship?

	a. Foreign key
Is it necessary to have an identity column in a table?

	No, a combined key is possible. As long as it represents a unique identification.
What is the difference between LEFT OUTER JOIN and RIGHT OUTER JOIN?

	LEFT OUTER JOIN selects all rows from the left table no matter if there are a
	relation to the right table (if there’s no match the right side will be represented by NULL). RIGHT OUTER JOIN – the opposite.
Which keyword would you use to create a table alias in a SQL statement?

	AS
How will you implement a many-to-many relationship implemented in a database design?

	Via an intermediate table. Pivot talbe
What is ACID short for?

	Atomicity, Consistency, Isolation, Durability.

What signifies the end and beginning of a transaction?

	BEGIN TRANSACTION, COMMIT TRANSACTION (optional: ROLLBACK TRANSACTION)
What is the purpose of creating a trigger?

	To execute statements when a particular event occurs.
How will you retrieve a column value (i.e. Blogtitle) in a table of the row that has been created (i.e. in a trigger)?

	SELECT Blogtitle FROM inserted.
What is a self join?

	A relation from a table to itself. Is used i.e. when building up hierarchies
What is cardinality?

	One-One, One-Many, Many-Many
What does a FULL OUTER JOIN do?

	Selects all rows from both tables no matter if there are a match.
Which keyword is used when you want to remove a procedure?

	DROP
How is an aggregation filtered? (i.e. only output those rows with a minimum value)

	HAVING. The order of keywords is SELECT, FROM, JOIN, WHERE, GROUP BY, HAVING, ORDER BY
How can you tell the difference between a parameter and a local variable in a procedure?

	Parameter is placed before AS. Local variable is placed after AS and needs to be declared with the DECLARE keyword. Both have @ as prefix.
Name five important aggregate functions in SQL

	SUM, AVG, MIN, MAX, COUNT
How is a user-defined type created?

	CREATE TYPE. Used i.e. to pass a table as a parameter to a procedure.
What does the @@ERROR do?

	Returns the error number for the last T-SQL statement executed.
What does the @@IDENTITY do?

	Returns the last-inserted identity value.
What does AVG() do?

	Returns the average of the values in a group. Null values are ignored.
How do you select everything from a table (i.e. BlogPost)?

	SELECT * FROM BlogPost;
Why is Crow’s foot called Crow’s foot?

	Because the many-side of a relationship looks like a crow’s foot.
What is SQL short for?

	Structured Query Language
How do you get your results from the column Price with the lowest price first and then up in price?

	ORDER BY Price ASC
What is the purpose of creating a transaction?

	To be able to execute a number of statements with the possibility to roll them back in case one of them fails
What does SUM() do?

	Returns the sum of all the values in the expression. SUM can be used with numeric columns only. Null values are ignored.
