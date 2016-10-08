
[Repository](https://github.com/mikejakobsen/t-sql)

### Search

Where <table> LIKE 'Random%' Search

	Use Chinook;

	Select * from Artist
	where Name Like 'AC%'

###Wildcard

Add % to beginning or end.

### Substring
The first 5 characters in name. A way to make an exert of a blog post.

	Select Name, SUBSTRING(Name, 1, 5)
	From Artist;

### Replace

	Use Chinook;

	Select *, REPLACE (Title, 'Blood', 'B******d') as CleanTitle
	From Album where title Like '%Blood%';

###Index

	Create index idx_artist_name on Artist(Name);

