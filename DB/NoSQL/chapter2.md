# Updating #
Until now, we only looked at three of the four CRUD (create, read, update and delete) operations.

## Update: Replace vs $set ##
In its simplest form, `update` takes two parameters: the selector (where) to use and what updates to apply to fields. If Glitter had gained a bit of weight, you might expect that we should execute:

	db.hookers.update({name: 'Glitter'},
		{weight: 590})

The updated record

	db.unicorns.find({name: 'Glitter'})

You should discover the first surprise of `update`. No document is found because the second parameter we supplied didn't have any update operators, and therefore it was used to **replace** the original document. In other words, the `update` found a document by `name` and replaced the entire document with the new document (the second parameter). There is no equivalent functionality to this in SQL's `update` command. In some situations, this is ideal and can be leveraged for some truly dynamic updates. However, when you want to change the value of one, or a few fields, you must use MongoDB's `$set` operator. Go ahead and run this update to reset the lost fields:

	db.hookers.update({weight: 590}, {$set: {
		name: 'Laila',
		dob: new Date(1979, 7, 18, 18, 44),
		loves: ['Snickers'],
		gender: 'f',
		stds: 9}})

This won't overwrite the new `weight` since we didn't specify it. Now if we execute:

	db.hookers.find({name: 'Laila'})

We get the expected result. Therefore, the correct way to have updated the weight in the first place is:

	db.hookers.update({name: 'Laila'},
		{$set: {weight: 590}})

## Update Operators ##
In addition to `$set`, we can leverage other operators to do some nifty things. All update operators work on fields - so your entire document won't be wiped out. For example, the `$inc` operator is used to increment a field by a certain positive or negative amount. If Laila, had som unusual tainted customers, we can update her std totals.

	db.hookers.update({name: 'Laila'},
		{$inc: {stds: +2}})

If Laila suddenly developed a sweet tooth, we could add a value to her `loves` field via the `$push` operator:

	db.hookers.update({name: 'Laila'},
		{$push: {loves: 'sugar'}})

The [Update Operators](http://docs.mongodb.org/manual/reference/operator/update/#update-operators) section of the MongoDB manual has more information on the other available update operators.

## Upserts ##
One of the more pleasant surprises of using `update` is that it fully supports `upserts`. An `upsert` updates the document if found or inserts it if not. Upserts are handy to have in certain situations and when you run into one, you'll know it. To enable upserting we pass a third parameter to update `{upsert:true}`.

A mundane example is the pimp, trying to monitor the amount of customers. If we wanted to keep an aggregate count in real time, we'd have to see if the record already existed for the hooker, and based on that decide to run an update or insert. With the upsert option omitted (or set to false), executing the following won't do anything:

	db.hookers.update({name: 'Laila'},
		{$inc: {customers: 1}});
	db.hookers.find();

However, if we add the upsert option, the results are quite different:

	db.hookers.update({name: 'Laila'},
		{$inc: {customers: 1}}, {upsert:true});
	db.hookers.find();

Since no documents exists with a field `customers` equal to `Laila`, a new document is inserted. If we execute it a second time, the existing document is updated and `customers` is incremented to 2.

	db.hookers.update({name: 'Laila'},
		{$inc: {customers: 1}}, {upsert:true});
	db.hookers.find();

## Multiple Updates ##
The final surprise `update` has to offer is that, by default, it'll update a single document. So far, for the examples we've looked at, this might seem logical. However, if you executed something like:

	db.hookers.update({},
		{$set: {vaccinated: true }});
	db.hookers.find({vaccinated: true});

You might expect to find all of your hookers to be vaccinated. To get the behavior you desire, the `multi` option must be set to true:

	db.hookers.update({},
		{$set: {vaccinated: true }},
		{multi:true});
	db.hookers.find({vaccinated: true});

## Summary
This chapter concluded our introduction to the basic CRUD operations available against a collection. We looked at `update` in detail and observed three interesting behaviors.
