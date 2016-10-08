# Mongoose

### Mongo Excercise

a) db.getCollection("retaurants").find({name:"Mackies"})

b) db.getCollection("retaurants").find({cuisine:"Italian", "grades.score" : {$gt : 50}}).sort({"adress.zipcode" : 1})

c) db.retaurants.update({"restaurant_id" : "55555555"}, {$push : {grades : {date : Date(), grade : "A", score : 666}}})

d) db.retaurants.remove({cuisine : "Hamburgers", "grades.score" : {$lt : 1}})

# Mongoose

```
	var mongoose = require('mongoose');
	var Schema = mongoose.Schema;
```
First Mongoose is required to the Mongoose variable - then the Schema objekt within mongoose.
Is saved as the Schema variable.

Read more: http://mongoosejs.com/docs/guide.html

Snippet: https://github.com/nashio/vim-snippets

# ODM tool

A document in Mongoose is an instance of the model.

´´
var Person = mongoose.model('Person', personSchema);

	var bob = new Person({
		firstName: 'Bob',
		lastName: 'Doe'
	});
´´

A new is then defined - Blog is the modelname - blogSchema is the defined Schema

var Blog = mongoose.model('Blog', blogSchema);
// ready to go!

´´
var mongoose = requre('mongoose');
var Schema = mongoose.Schema;

	var customerSchema = new Schema({
		name: String,
		address: String,
		city: String,
		state: String,
		country: String,
		zipCode: Number,
		createdON: Date,
		isActive: Boolean,
	})
´´

var simpleSchema = new Schema({ fieldName: SchemaType});

## Data types

| Mongoose Schema Types | Javascript Data Types |
|-----------------------|-----------------------|
| String                | String                |
| Number                | Number                |
| Date                  | Object                |
| Buffer                | Object                |
| Boolean               | Boolean               |
| Mixed                 | Object                |
| ObjecId               | Object                |
| Array                 | Array(Object)         |

# Basic Schema

```
	/* Example */

	var mongoose = require('mongoose');
	var Schema = mongoose.Schema;

	// child address schema...
	var addressSchema = new Schema ({
	    type: String,
	    street: String,
	    city: String,
	    state: String,
	    country: String,
	    postalCode: Number,
	});

	// parent customer schema..
	var customerSchema = new Schema ({
	    name: {
	        first: String,
	        last: String
	},
	    // Adresse Schema is defined at the top
	    address: [ addressSchema ],
	    createdOn: { type: Date, default: Date.now },
	    isActive: { type: Boolean, default: true },
});
```

# Model Example

```
	/* Model Example - to remember the syntax */

	var mongoose = require('mongoose');
	var Schema = mongoose.Schema;

	// Pre-define sub-documents..
	var subCategory = new Schema({
	    name: String,
	    description: String,
	    isActive: Boolean});

	var subAnswers = new Schema({
	    answerText: String,
	    isCorrect: Boolean,
	    displayOrder: Number});

	var subQuestions = new Schema({
	    type: { type: String},
	    text: String,
	    answers: [ subAnswers ] });

	// Define main document schema..
	var quizSchema = new Schema({
	    name: String,
	    description: String,
	    categories: [ subCategory ], 
	    questions: [ subQuestions ] 
	});
```

# Query

## Find

```
	// No callback - Deferrd execution
	var query = Standup.find();

	// With callback - Executes query immediately
	Standup.find(function (err, results) {
	    // handle the error - or the results here
	});


	// With callback - Executes query immediately
	// Select * from Standup where MamberName = 'David'

	Standup.find({ MamberName: 'David'}, function (err, results) {
	    // handle the error - or the results here
	};

	    // Select * from Standup where MamberName = 'David'
	    // Only return row MamberName and impediment
	    Standup.find({ MamberName: 'David'}, 'MemberName impediment',
	        function (err, results) {
	            // handle the error - or the results here
};
```
## Find One

```
	// No callback - No Conditions

	var query = Standup.findOne();
	query.exec(function (err, results) {
	    // handle the error - or the result
	});

	// Calling the exec function on the query - prints the result


	// With conditions..

	var query = Standup.findOne({ MemberName: 'Mark'});

```

## Find By Id

```
	// Search parameter saved as a variable
	var id = '57e8f6e755ce973a83e1ef55';
	var Standup = require('../models/standup.server.model.js');

	// By id - No conditions
	// Takes the previous defined ID variable as a parameter

	var query = Standup.findById(id);
	query.exec(function (err, doc) {
	    // Error or the result
	});

	// Chained method calls version
	Standup.findById(id).exec(function (err, results) { .... });

	// By id.. - Every field except 'impediment'
	var query = Standup.findById(id, '-impediment');
```

## Find where

Chain where methods together

Where discount is greather then 10 and less then 20 - and zipCode is 12345

```
	// Chain where methods together
	// Where discount is greather then 10 and less then 20 - and zipCode is 12345
	Customer.where('discount').gte(10).lt(20)
			.where('zipCode', '12345')
			.exec(function(err, results) {
				if (err) throw err;
				console.log(results);
			});
```

| $gt  | greater than             |
|------|--------------------------|
| $gte | greater than or equal to |
| $in  | exists in                |
| $lt  | less than                |
| $lte | less than or equal to    |
| $ne  | not equal to             |
| $nin | does not exist           |

# Updating

Find by id - Id parameter
Then updating the found object

Makes two calls to the db, first it finds the document, then updates it.

```
	// Find by id - Id parameter
	// Then updating the found object

	Standup.findById(id).exec(function (err, doc) {
		// handle any errors
		if (err) return errorHandler(err);

		// Update the found document - impediment field
		doc.impediment('None');
		doc.save(function (err) {
			if (err) return errorHandler(err);
			console.log('This shit was updated');
		});
	)};
```

Best practice
 Specity the query condition
 And then the updated info
```
	var condition = { memberName: 'Mary' };
	var update = { impediment: 'None - some shit' };

	Standup.update(condition, update, function(err, numberAffected, rawResponse) {
		// Error or result - same old
	});

	// Finding a document - then updating it.
	Standup.findOne({ memberName: 'Mary'}, function(err, doc) {
		// Handle errors here - Validate document results
		doc.impediment = 'None - Updated the impediment object';
		doc.save(function (err) {
			// Handle Errors
		});
	});
```

# Validation

| Schema Type | Built-in Validators                |
|-------------|---------------------|------|-------|
| String      | required            | enum | match |
| Number      | required            | min  | max   |
| Date        | required            |      |       |
| Buffer      | required            |      |       |
| Boolean     | required            |      |       |
| Mixed       | required            |      |       |
| ObjectId    | required            |      |       |
| Array       | required            |      |       |

Required validation example

Name should be a Sting, and is required

```

	var customerSchema = new Schema({
		name:       { type: String, required: true},
		address:    String,
		city:       String,
		state:      String,
		country:    String,
		zipCode:    number,
		createdOn:  Date,
		isActive:   Boolean,
	});

	// After the schema is defined
	customerSchema.path('city').required(true, 'Ingen by');
	// The path is the field name.

	/* Regular Expresion */

	var reMatch = /[a-zA-Z]/;
	var customerSchema = new Schema({
		name: {type: String,
				required: true;
				match: reMatch },
	});

	// Defining a regex as the reMatch, then applying it as a match parameter.

	/* Enum validation */

	// String - enum validation - should match within the array
	var array = ['none', 'minor', 'blocking', 'severe'];

	var standupSchema = new Schema ({
		impediments: { type: String,
						required: true,
						enum: array }
	}
```

## Basic number validation

Customer must receive at least a 5% discount

```
	var customerSchema = new Schema({
		name: String
		discount: { type: Number, min: 5 }
	});

	// At least 60%
	var customerSchema = new Schema({
		name: String
		discount: { type: Number, max: 60 }
	});
```
# Middleware

```
	// Middleware flow

	var personSchema = new Schema({
		firstName: { type: String, required: true },
		lastName: { type: String, required: true },
		status: { type: String, required: true, default: 'Alive' }
	});
	// If no value is applied, the default is 'Aliv'

	// Build a model from the person schema
	var Person = new.mongoose.model.('Person', personSchema);

	// New document instance of a person model
	var newPerson = new Person( { firstNAme: 'John', lastName: 'Doe'} );

	// Save the document - Validation (required) is instanciated now
	newPerson.save(function (err) {
		if (err) return handleError(err);
		// Saved the person
	});

```

### FLow

Save -> Defaults Applied -> Validation -> Error

# Custom validation

```
	// Custom validation - method signature = validation(obj, [ErrorMsg])

	var sizeValidator = [
		function (val) {
			return (val.length > 0 && val.length <= 50)
			// Lenght is more then 0 and less than or equal to 50
			// Mere end 0, mindre eller det samme som 50
		},
		// Custom error text
		'String must be between 1 and 50 characters long' ];
	var personSchema = new Schema({
		firstName: { type: String, required: true, validate: sizeValidator },
		lastName:  { type: String, required: true, validate: sizeValidator },
		status:    { type: String, required: true, default: 'Alive' },
	});
```
