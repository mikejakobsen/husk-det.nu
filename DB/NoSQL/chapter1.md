# Chapter 1 - Fuck SQL

1. MongoDB has the same concept of a `database` with which you are likely already familiar.

2. A database can have zero or more `collections`. A collection shares enough in common with a traditional `table` that you can safely think of the two as the same thing. `Collections = Table`

3. Collections are made up of zero or more `documents`. Again, a document can safely be thought of as a `row`. `Documents = row`

4. A document is made up of one or more `fields`, which you can probably guess are a lot like `columns`. `Field = Columns`

5. `Indexes` in MongoDB function mostly like regular indexes.

6. `Cursors` are different than the other five concepts but they are important enough, and often overlooked, that I think they are worthy of their own discussion.  The important thing to understand about cursors is that when you ask MongoDB for data, it returns a pointer to the result set called a cursor, which we can do things to, such as counting or skipping ahead, before actually pulling down data.

To recap, MongoDB is made up of `databases` which contain `collections`. A `collection` is made up of `documents`. Each `document` is made up of `fields`. `Collections` can be `indexed`, which improves lookup and sorting performance. Finally, when we get data from MongoDB we do so through a `cursor` whose actual execution is delayed until necessary.

	To start Mongo with a db defined. `Mongo database`
	To clear the terminal window use `cls` acts like clear

## SQL vs Not-Only-SQL
The core difference comes from the fact that relational databases define `columns` at the `table` level whereas a document-oriented database defines its `fields` at the `document` level. That is to say that each `document` within a `collection` can have its own unique set of `fields`.  As such, a `collection` is a dumbed down container in comparison to a `table`, while a `document` has a lot more information than a `row`.
> A collection acts as a container of data.


Global commands - `help` or `exit`. Commands that you execute against the current database are executed against the `db` object, such as `db.help()` or `db.stats()`. Commands that you execute against a specific collection, which is what we'll be doing a lot of, are executed against the `db.COLLECTION_NAME` object, such as `db.hookers.help()` or `db.hookers.count()`.

A small side note: Because this is a JavaScript shell, if you execute a method and omit the parentheses `()`, you'll see the method body rather than executing the method.

First we'll use the global `use` helper to switch databases, so go ahead and enter `use learn`. It doesn't matter that the database doesn't really exist yet. The first collection that we create will also create the actual `learn` database. Now that you are inside a database, you can start issuing database commands, like `db.getCollectionNames()`. If you do so, you should get an empty array (`[ ]`). Since collections are schema-less, we don't explicitly need to create them. We can simply insert a document into a new collection. To do so, use the `insert` command, supplying it with the document to insert:

	db.hookers.insert({name: 'Glitter',
		gender: 'f', weight: 450})

Result

	{
		"_id": ObjectId("5744a91040765d171766294a"),
		"name": "Glitter",
		"gender": "f",
		"weight": 450
	}

The above line is executing `insert` against the `hooker` collection, passing it a single parameter. Internally MongoDB uses a binary serialized JSON format called BSON. Externally, this means that we use JSON a lot, as is the case with our parameters. If we execute `db.getCollectionNames()` now, we'll actually see two collections: `hookers` and `system.indexes`. The collection `system.indexes` is created once per database and contains the information on our database's indexes.

You can now use the `find` command against `hookers` to return a list of documents:

	db.unicorns.find()

Notice that, in addition to the data you specified, there's an `_id` field. Every document must have a unique `_id` field. You can either generate one yourself or let MongoDB generate a value for you which has the type `ObjectId`. Most of the time you'll probably want to let MongoDB generate it for you. By default, the `_id` field is indexed - which explains why the `system.indexes` collection was created. You can look at `system.indexes`:

	db.system.indexes.find()

What you're seeing is the name of the index, the database and collection it was created against and the fields included in the index.

Now, back to our discussion about schema-less collections. Insert a totally different document into `hookers`, such as:

	db.hookers.insert({name: 'Starlight',
		gender: 'f',
		home: 'Amsterdam',
		std: false})

And, again use `find` to list the documents. Once we know a bit more, we'll discuss this interesting behavior of MongoDB, but hopefully you are starting to understand why the more traditional terminology wasn't a good fit.

## Mastering Selectors ##
In addition to the six concepts we've explored, there's one practical aspect of MongoDB you need to have a good grasp of before moving to more advanced topics: query selectors. A MongoDB query selector is like the `where` clause of an SQL statement. As such, you use it when finding, counting, updating and removing documents from collections. A selector is a JSON object, the simplest of which is `{}` which matches all documents. If we wanted to find all female hookers, we could use `{gender:'f'}`.

Before delving too deeply into selectors, let's set up some data to play with. First, remove what we've put so far in the `hookers` collection via: `db.hookers.remove({})`. Now, issue the following inserts to get some data we can play with (I suggest you copy and paste this):

	db.hookers.insert({name: 'Horny',
		dob: new Date(1992,2,13,7,47),
		loves: ['Sunshine,'Watching TV'],
		weight: 600,
		gender: 'f',
		stds: 3});
	db.hookers.insert({name: 'Aurora',
		dob: new Date(1991, 0, 24, 13, 0),
		loves: ['French', 'Grape fruits'],
		weight: 450,
		gender: 'f',
		stds: 7});
	db.hookers.insert({name: 'Laila',
		dob: new Date(1973, 1, 9, 22, 10),
		loves: ['Anal', 'Redbull'],
		weight: 984,
		gender: 'f',
		stds: 82});

Now that we have data, we can master selectors. `{field: value}` is used to find any documents where `field` is equal to `value`. `{field1: value1, field2: value2}` is how we do an `and` statement. The special `$lt`, `$lte`, `$gt`, `$gte` and `$ne` are used for less than, less than or equal, greater than, greater than or equal and not equal operations. For example, to get all male unicorns that weigh more than 700 pounds, we could do:

	db.hookers.find({gender: 'f',
		weight: {$gt: 700}})
	/// $ne - not equal
	/// $gte - greater than or equal
	db.hookers.find({gender: {$ne: 'm'},
		weight: {$gte: 701}})


The `$exists` operator is used for matching the presence or absence of a field, for example:

	db.hookers.find({
		stds: {$exists: false}})

should return a single document. The '$in' operator is used for matching one of several values that we pass as an array, for example:

    db.hookers.find({
    	loves: {$in:['Anal,'Redbull']}})

If we want to OR rather than AND several conditions on different fields, we use the `$or` operator and assign to it an array of selectors we want or'd:

	db.hookers.find({gender: 'f',
		$or: [{loves: 'Anal},
			  {weight: {$lt: 500}}]})

The above will return all female hookers which either love Anal or weigh less than 500 pounds.

There's something pretty neat going on in our last two examples. You might have already noticed, but the `loves` field is an array. MongoDB supports arrays as first class objects. This is an incredibly handy feature. Once you start using it, you wonder how you ever lived without it. What's more interesting is how easy selecting based on an array value is: `{loves: 'watermelon'}` will return any document where `watermelon` is a value of `loves`.

Too see more selectors [Query Selectors](http://docs.mongodb.org/manual/reference/operator/query/#query-selectors).

We've seen how these selectors can be used with the `find` command. They can also be used with the `remove` command which we've briefly looked at, the `count` command, which we haven't looked at but you can probably figure out, and the `update` command which we'll spend more time with later on.

The `ObjectId` which MongoDB generated for our `_id` field can be selected like so:

	db.hookers.find(
		{_id: ObjectId("TheObjectId")})

## Summary
We haven't looked at the `update` command yet, or some of the fancier things we can do with `find`. However, we did get MongoDB up and running, looked briefly at the `insert` and `remove` commands. We also introduced `find` and saw what MongoDB `selectors` were all about.
