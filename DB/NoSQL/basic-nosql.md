
# First - JSON

JSON syntax is a subset of the JavaScript object notation syntax:
* Data is in name-value pairs
		{
			"company": Volkswagen
		}
* Data is separated by commas
		{
			"company": "Volkswagen",
			"name": "Beetle"
		}

* Curly braces hold objects
* Square brackets hold arrays
		{
			"company": "Volkswagen",
			"Cars": [{
				"Color": "Red",
				"Color": "Grey",
				"Color": "Yellow"
			}]
		}

### Cheatsheet

	$gt:		Greather than
	$gte:		Greather than or equal
	$lt:		Lower than
	$lte:		Lower than or equal
	$all:		All
	$in:		In (in the array)
	$nin		Not in (in the array)
	$ne:		Not equal

### Sort

Append .sort()
Append .sort()

	db.hooker.find().sort( {number: 1 })
