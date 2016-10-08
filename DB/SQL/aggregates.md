[Repository](https://github.com/mikejakobsen/t-sql)

# Aggregates

[Data from my repository](https://github.com/mikejakobsen/t-sql)

#Sum
Calculates the sum of all the fields.

	use Chinook;

	select sum(Total) as AllTimeSales from Invoice;

Average

	select avg(Total) as AvgSale from Invoice;

Basic

	Select
	Sum(Total) as AllTimeSales,
	Avg(Total) as AvgSale,
	Count(Total) as SalesCount,
	Min(Total) as SmallestSale,
	Max(Total) as Maxsale
	from Invoice;

###Custom stuff

Define aggregate(field * field)

	SUM(UnitPrice * Quantity) as SalesTotal,
	AVG(UnitPrice * Quantity) as AvgPurchase

### In - Defines a list of

IN('Germany', 'Argentina')

	Select BillingCountry,
	SUM(UnitPrice * Quantity) as SalesTotal
	from Invoice
	inner join InvoiceLine on InvoiceLine.InvoiceId = Invoice.InvoiceId
	Where BillingCountry IN('Germany', 'Argentina', 'United Kingdom')
	Group by BillingCountry
	order by BillingCountry;
