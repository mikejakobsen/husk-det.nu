# Transactions

[Repository](https://github.com/mikejakobsen/t-sql)

A common example of a transaction is the process of transferring money from a checking account to a savings account. This involves two operations: deducting money from the checking account and adding it to the savings account. Both must succeed together and be committed to the accounts, or both must fail together and be rolled back so that the accounts are maintained in a consistent state.

### ACID

By using a transaction, both the operations, namely debit and credit, can be guaranteed to succeed or fail together. So both accounts remain in a con- sistent state all the time.


###When to use Transactions?

* In batch processing, where multiple rows must be inserted, updated, or deleted as a single unit
* Whenever a change to one table requires that other tables be kept consistent
* When modifying data in two or more databases concurrently
* In distributed transactions, where data is manipulated in databases on different servers


When you use transactions, you place locks on data pending permanent change to the database. No other operations can take place on locked data until the lock is released.

* BEGIN TRANSACTION
* COMMIT TRANSACTION
* COMMIT WORK
* ROLLBACK TRANSACTION
* ROLLBACK WORK
