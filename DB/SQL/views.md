[Repository](https://github.com/mikejakobsen/t-sql)

#Views


On the surface, a view is nothing more than a SELECT query that is saved with a name in a database.

**Code Reuse** Saves common queries into objects.

**Pros:** Code Reuse

The ability to hide all of the query logic, exposing a simple object/table.

	CREATE VIEW vProductCosts
	AS
	SELECT ProductID, Name, StandardCost
	FROM Production.Product

To select all from the *View*

	SELECT * FROM vProductCosts

Alter view

	ALTER VIEW vProductCosts
	AS
	SELECT ProductID, ProductSubcategoryID, Name, ProductNumber, StandardCost
	FROM Production.Product

### Summary

*Hiding Complexity*

One of the most common, and arguably one of the most important, reasons to use views is to simplify data access. In a normalized database, even the most basic queries can involve many different tables.

View can update data, but should not.
