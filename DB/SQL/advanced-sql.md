[Repository](https://github.com/mikejakobsen/t-sql)

# Advanced shit

## Variables:
Simply variable are buckets where you can store values. T-SQL support variables. You can use variable to assign values, change values when you need. In T-SQL variables declare, assign values, retrieving values as below as below.

	**Syntex:**
	---declaration
	DECLARE @variable data-type

	---Assignment
	SET @variable='value'

	---Retrieve
	SELECT @variable

	Example

	DECLARE @MyName varchar(20)
	SET @MyName='Mike'
	SELECT @MyName
	2)GO

When we execute SQL statement they will execute one by one. But in T-SQL we have a facility to execute bunch of statement at a time. Known as Batch Execution. The main use of this is performance. Also if any one statement have syntactical error in any one statement, none of the statement will execute.

**Example**

	USE StudentDb
	INSERT INTO Student(name,email) VALUES ('Mike','mike@jakobsen.dk');

	SELECT * FROM Student;
	GO

##IF..ELSE

	DECLARE @temp char(2)
	SELECT @temp = Gender
	FROM Student
	WHERE StudentId=333
	IF @temp='M'
	PRINT 'Hi dude. How are you today?'
	ELSE
	PRINT 'Hi beautiful. How are you today?'

##BEGIN...END

BEGIN,END keyword are much like open and close parenthesis we use in high-level programming languages. Mostly we use these in condition statement where we have more than one statement to execute.

	IF (SELECT COUNT(*) FROM Student WHERE Gender = 'M') > 0
	BEGIN
	PRINT 'This is a boys college'
	PRINT ' '
	SELECT fname, lname
	FROM Student
	END
	ELSE
	PRINT 'No boys'

##WHILE
WHILE is used to repeatedly execute statements until the condition became false. There are many advantage of using while in T-SQL.

	DECLARE @counter INT,@total INT
	SET @counter=0
	SET @total=SELECT COUNT(*) FROM Student WHERE lname="Gates"
	WHILE (@counter < @total)
	BEGIN
	UPDATE Student
	SET lname= lname + CAST(@counter as char(2))
	WHERE lname="Gates"
	SET @counter = @counter+1
	END

In this example we finding all record with last name as Gates and updating them with count appending to them.

##CASE
In programming we use case instead of multiple nested if..else statements. Similarly we use CASE to evaluate an express whether it is true or false based on it uses specific values.

	SELECT latitude , 'For Country' =
	CASE
	WHEN 'AF' THEN 'Afghanistan'
	WHEN 'IN' THEN 'India'
	WHEN 'RU' THEN 'Russia'
	WHEN 'US' THEN 'United States'
	WHEN 'LK' THEN 'Sri Lanka'
	END
	FROM Country

##Transactions
Transactions are much like a batch. The only difference between Transaction and batch is we can undo all changes we made by statements with in the single translations. We can not do this in Batch. We use COMMIT TRANSACTION or ROLLBACK TRANSACTION statement at end. If we put ROLLBACK TRANSACTION it will undo all the changes.

	BEGIN TRANSACTION
	SELECT * FROM Student
	......
	COMMIT TRANSACTION

To undo a transaction.

	ROLLBACK TRANSACTION;

##Stored Procedures

#### [Empiri](http://www.mikejakobsen.com/pdf/stored.pdf)


Stored Procedures are most Important concept in databases. There are many advantages using stored procedures. Mainly performance because sorted procedures are compiled and will be cashed after first time we used it. Another big advantage is we can call stored procedures form programming languages like c#.

As with views, stored procedures can be used to return a result set based on a *SELECT* statement.

	CREATE PROCEDURE Mike
	 @sname nvarchar(50)
	AS
	SELECT fname,lname,rolno
	  FROM Student
	WHERE fname = @sname
	GROUP BY fname
	ORDER BY fname
	GO

	**Exectue stored procedure**
	EXECUTE usp 'Mike'
	GO

