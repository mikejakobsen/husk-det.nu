
[Repository](https://github.com/mikejakobsen/t-sql)

# T-SQL


### Notes

-- To add comments in T-SQL

Right-click table and select Design. To add fields.

Update intellisense Ctrl+Shift+R

<F5> to run the query.

### Creating table

	USE BigMachine;

	CREATE TABLE Users(
		email varchar(50)
	);

Each row, is just a comma separated list.

Dropping

	DROP TABLE Users;


### Guid

	USE BigMachine;

	DROP TABLE dbo.Users;

	CREATE TABLE Users(
		Id uniqueidentifier primary key
		);

### Auto-incremented primary-key

*identity(1,1) Start at one, increment by one.

	CREATE TABLE Users(
		Id integer primary key identity(1,1),
			email varchar(50)
			);

### Composit primary-key

Many-to-many relationship. Users_Roles acts like a pivot-table to avoid many-to-many.

UserRoles - defines that each user can only have one role.
**Two primary keys (UserId, RoleId)**

	create table Users_Roles(
		UserId integer,
		RoleId integer,
		primary key(UserId,RoleId)
		);

	create table Users(
	id integer primary key identity(1,1),
		email varchar(50) not null
		);

	create table Users_Roles(
	UserId integer,
		RoleId integer,
		);

	create table Roles(
	Id integer primary key identity(1,1),
		Name varchar(50)
		);

### Update
Update the first 10 users.

	Update dbo.Customer
	set FirstName = 'Idiot'
	where CustomerId <= 10;

Example 2

	UPDATE dbo.Products
	SET ProductName = 'Flat Head Screwdriver'
	WHERE ProductID = 50
	GO

### Delete

Delete the before mentioned users.

	delete from Customer
	where CustomerId <= 10;

### Query

	use Chinook;
	select FirstName,Lastname, Email
	From customer;

### Alias

	select FirstName as 'First',
	LastName as 'Last name'

Group First + Lastname into FullName

	select FirstName + ' ' + LastName as FullName,
	Email,Country
	from Customer;

### Inner join

1. **Select** - The required tables.
2. **From** - Defines the source table
3. **Inner join** - the other source table
4. **On** - Defines the condition, which table should match.

Example

	Select Invoice.InvoiceId,InvoiceDate
	from Invoice
	Inner join InvoiceLine
	on InvoiceLine.InvoiceId = Invoice.InvoiceId;

	Select Name, Title
	From Artist
	Inner join Album
	On Album.ArtistId = Artist.ArtistId;

### Subqueries
A query within a query.

	select *,
	(select count(1) from Album where Album.ArtistId = Artist.ArtistId) as AlbumCount
	from Artist;

### Left join

The LEFT JOIN keyword returns all rows from the left table *(Artist)*, with the matching rows in the right table *(Album)*. The result is NULL in the right side when there is no match.

	Select Name, Title
	From Artist
	left join Album
	On Album.ArtistId = Artist.ArtistId;

This query returns all Artist and null in album, when the artist has no albums associated.

**Right Join** does the opposit. Shows *Album* with out an artist.

### Full Join

A full join returns all the values. Everything that is included in the select statement.

	Select Name, Title
	From Artist
	Full join Album
	On Album.ArtistId = Artist.ArtistId;

### Ordering results
Always order your queries.

Order by *Field* desc/asc.

Example

	Select FirstName + ' ' + LastName as Name, Email, Country,
	InvoiceId, InvoiceDate, Total
	From Customer
	Inner join Invoice on Invoice.CustomerId = Customer.CustomerId
	Where country = 'Brazil'
	Order by Total desc;

Limit to Top 5

	Select Top 5 Firstname ...

Or ..

	Where country = 'Denmark'
	Or Country = 'Canada'

### Filtering
And where total is larger then 5.

Where (country = 'Denmark'
Or Country = 'Canada')
And Total > 5
Order by Total desc;

### Offsetting result
You have to use *order by* with offset.

	Offset 1 rows;

Removes the first result from the query.
