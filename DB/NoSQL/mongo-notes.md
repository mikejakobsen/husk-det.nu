# Walkthrough

[Great page SQL vs. NoSQL](https://medium.com/@pranava_swaroop/sql-to-mongodb-mapping-chart-2d7ccda5dbaf#.adwjwcvmt)


	db.bibliotek.insert{}



Insert a variable into a collection.

	var bog =

	{
		"ISBN" : 12.0,
			"titel" : "Birdsong",
			"forfatter" : "Sebastian Faulk",
			"format" : "ebog",
			"pris" : 6.0
	}

	db.bibliotek.insert({bog})

Save it. Another way to insert collections.

	db.bibliotek.save(
	{
		"ISBN" : 13.0,
			"titel" : "The Woman in White",
			"forfatter" : "Wilkie Collins",
			"format" : "ebog",
			"pris" : 9.0
	})

Another book

	db.bibliotek.save(
		{
		"ISBN" : 13.0,
		"titel" : "Lord of the Flies",
		"forfatter" : "William Golding",
		"format" : "ebog",
		"pris" : 9.0
		})

Query - Where ISBN: 13

	db.('bibliotek').find({ISBN:13})

Query - Where ISBN i greater than 1, and less than 13.
	db.bibliotek.find({ISBN: {$gt: 1, $lt: 13}})

Update - Change ISBN from 13 to 18

	db.bibliotek.update({ISBN: 13}, {$set:{ISBN: 18}})

Sort

	db.bibliotek.find().sort({ISBN:-1})

In

	db.bibliotek.find({ISBN: {$in: [1,2,15]}})

Comparison SQL vs MongoDB

	|          |          |
	|----------|----------|
	| WHERE    | $match   |
	| GROUP BY | $group   |
	| HAVING   | $match   |
	| SELECT   | $project |
	| ORDER BY | $sort    |
	| LIMIT    | $limit   |
	| SUM      | $sum     |
	| COUNT    | $sum     |
	| JOIN     | $unwind  |

### Aggregate

	db.ships.aggregate([$group: {_id: "Klingon Empire", skibe: {$sum: "$crew"}}}])

# SQL vs. MongoDB

	database                -->            database
	table                   -->            collection
	row                     -->            document or BSON document
	column                  -->            field
	index                   -->            index
	table joins             -->            embedded documents & linking
	where                   -->            $match
	group by                -->            $group
	count()                 -->            $sum
	order by                -->            $sort
	limit()                 -->            $limit
	select                  -->            $project
	having                  -->            $match

### Alter a table (SQL)/Collection(MongoDB)

	SQL:
		 Alter table users ADD location Varchar(255);

	MongoDB:
		 db.users.update(
			 {user_id: 'Pranava'},
			 {$set: { location: 'Bharat'},
			 {multi: true}
		 )


Select specific columns in a table(Sql)/Collection(MongoDB)

SQL:

			 SELECT user_id, age from users;

MongoDB:
		 db.users.find({},{user_id: 1, status: 1});

Add a condition to choose which field you want to output.

		db.bibliotek.find({ISBN: 1}, {titel: 1})

And query

SQL:
		SELECT * from bibliotek where titel='Lord of the Flies' and format='ebog';

MongoDB

		db.bibliotek.find({titel: "Lord of the Flies",format: "ebog"})

Query Or - The titel is either *The Great Gatsby* or the author is 	*F Scott Fitzgerald*.

	db.bibliotek.find({$or: [{titel: "The Great Gatsby"}, {forfatter: "F Scott Fitzgerald"}]})
