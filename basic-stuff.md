# Meteor Basic




### Minimongo

Meteor uses Minimongo for caching. It's a in-browser database. To export from it *{}*

It's a client-side MongoDB implementation which supports basic queries, including some geospatial ones.


## Usage

Minimongo is designed to be used with browserify.

```javascript

// Require minimongo
var minimongo = require("minimongo");

var LocalDb = minimongo.MemoryDb;

// Create local db (in memory database with no backing)
db = new LocalDb();

// Add a collection to the database
db.addCollection("animals");

doc = { species: "dog", name: "Bingo" };

// Always use upsert for both inserts and modifies
db.animals.upsert(doc, function() {
	// Success:

	// Query dog (with no query options beyond a selector)
	db.animals.findOne({ species:"dog" }, {}, function(res) {
		console.log("Dog's name is: " + res.name);
	});
});
```


