# Functions

	CREATE FUNCTION <owner>.<funtion_name> (<parameters>)
	RETURNS <type>
	AS
	BEGIN
		<SQL Code>
	END


**Owner** = dbo


	CREATE FUNCTION dbo.GetSalesPrice (@wholesale_price smallmoney) RETURNS smallmoney
	AS
	BEGIN
		-- Declare a temporary value to hold our sales price
			DECLARE @salesprice smallmoney;

		-- Add on a 20% markup to the wholesale price
			SET @salesprice = @wholesale_price + @wholesale_price * 0.2;

		-- Add on 6.5% sales tax
			SET @salesprice = @salesprice + @salesprice * .065;

			RETURN (@salesprice);
	END;

Then later on call the function

	SELECT dbo.GetSalesPrice(1.00) AS ‘Sales Price’


	Sales Price
	---------------------
	1.278

	(1 row(s) affected)

