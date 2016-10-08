
[Repository](https://github.com/mikejakobsen/t-sql)

#Basic T-SQL

**3 Levels of interaction**

**Data Definition Language(DDL)
**

* 	Create
* 	Alter
* 	Drop
* 	Rename

**Data Manipulation Language (DML)**

* Select
* Insert
* Update
* Delete
* Truncate

**Data Control Language(DCL)
**

* Grant
* Revoke

## Create
Basic syntax

	CREATE TABLE <table-name>
	(
	 <attribute-name1>(<data-type>),
	 <attribute-name2>(<data-type>),
	 <attribute-name3>(<data-type>),
	 <attribute-name4> <data-type>
	)
Example

	CREATE TABLE Student
	(
	 stuid INT,
	 firstname VARCHAR(30) NOT NULL,
	 lastname VARCHAR(30) NOT NULL,
	 DOB DATE NOT NULL
	);

## Alter
Modifi table structure.

	**Adding New column to table**
	ALTER TABLE <table-name>
	ADD <column-name> <data-type>(length)

	**Modifying column**
	ALTER TABLE <table-name>
	MODIFY <column-name> <data-type>(lenght)

	**Removing column from table**
	ALTER TABLE <table-name>
	DROP COLUMN <column-table>
Example
	**Adding New join data column to student table**
	ALTER TABLE Student
	ADD JoinDate DATE

	**Modifying lastname colument datatype and length**
	ALTER TABLE Student
	MODIFY lastname nvarchar(40)

	**Removing column from table**
	ALTER TABLE Student
	DROP COLUMN firstname

## Drop
	DROP TABLE <table-name>
Example
	DROP TABLE Student
## Rename

Rename existing table

	RENAME <old-table-name> to <new-table-name>
Example

	RENAME Student to StudentIndex
## Select
Basic query

	SELECT <column-name> FROM <table-name>
Example:
	SELECT firstname, lastname FROM Student

## Insert
Insert
This command is used to add new row to the table.
Syntax:

	INSERT INTO <table-name>(col1,col2,....coln) VALUES (val1,val2,...valn)
Example:

	VALUES ('Mike','Jakobsen','6/8/1990")')
	INSERT INTO Student(firstname,lastname)

**Nvarchar**

*N'* defines the content as Nvarchar. ÆØÅ compatible.

	INSERT INTO dbo.Posts (Title, Body, UserId, StateId)
		VALUES
		(
			N'Brand new blog post',
			N'<p>Brand new content</p>',
			1,
			1 -- Draft
		);



## Update
This command is used to update data inside the table.

Syntax:

	UPDATE <table-name>
	SET <column1> = <value1>, <column2> = <value2>...., <column n> = <value n>
	WHERE <condition>;
Example:

	UPDATE Student
	SET DOB = "12/8/1990"
	WHERE firstname='Mike'

## Delete
This command is used to delete data from table.
Syntax:

	DELETE FROM <table-name>
	WHERE <condition>
Example:

	DELETE FROM Student
	WHERE firstname="Mike"

##Truncate
This command is used to delete complete data from table not structure.
Syntax:

	TRUNCATE TABLE <table-name>
Example

	TRUNCATE TABLE Student

##Grant
In general database admins uses this commands. Grant command used to give permission to user for certain operations.
Syntax:

	GRANT <privilege>
	ON    <object>
	TO    <user>
Example:

	GRANT CREATE TABLE
	TO user1;

##Revoke
This command is used to take back the previously allocated permissions from user.
Syntax:

	REVOKE <privilege>
	ON     <object>
	FROM   <username>
Example:

	REVOKE CREATE TABLE
	FROM user1
