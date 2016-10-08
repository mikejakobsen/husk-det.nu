
[Repository](https://github.com/mikejakobsen/t-sql)

# Creating Table
Users has a many-to-many relationship with Roles. Added a pivot table *(UserRole)* to avoid that.
References is required.

	UserId integer references Users(Id) on delete cascade,
	RoleId integer references Role(Id) on delete cascade

	create table Users(
		Id integer primary key not null identity(1,1),
		Email varchar(255) not null unique
		);

	create table Role(
		Id integer primary key not null identity(1,1),
		Name varchar(50) not null unique
		);

	create table UserRole(
		UserId integer references Users(Id) on delete cascade,
		RoleId integer references Role(Id) on delete cascade
		primary key(UserId,RoleId)
			);

### Inserting data

	Insert into Users(Email)
	values('mike@jakobsen.dk');

	insert into Role(Name)
	values('Admin');

	insert into UserRole(UserId,RoleId)
	values(1,1);

### Self contained query

	Use Chinook;

	select FirstName, LastName,
	(Select FirstName + ' '+LastName
	 from Employee bosses
	 where Employee.ReportsTo = bosses.EmployeeId) as Boss
	from Employee;

*Instead we can* inner join the tables on them self.

The Employess table gets to aliases **Workers and Boses**

	Use Chinook;

	Select workers.FirstName + ' '+ workers.LastName As Employee,
	bosses.FirstName + ' ' + bosses.LastName as Boss
	From Employee workers
	Left Join Employee as Bosses
	On workers.ReportsTo = Bosses.EmployeeId

### Subquery
	Select FirstName, LastName,
		(Select FirstName + ' ' + LastName
			From Employee as Chefer
			Where Employee.ReportsTo = Chefer.EmployeeId) as Chef
	from Employee;
