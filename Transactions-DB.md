# Database Transactions

A transaction is a unit of program execution that access and possible updates various data items in data database. it represents a single logical unit of work -> such as transferring funds from a checking account to a savings account -> where either all associted operations occur or none do

---
## ACID properties

To maintain database integrity, systems enforce four primary properties known as the **ACID** 
Described below

- **Atomicity** -> The *All-or-none* property guranteeing that either all operations of a transaction are executed properly in the database or none are. the **recovery** system enforces this by logging updates and rolling back incomplete operations.

- **Consistency** -> ensures that executing a transaction in isolation preserves oever all database correctness and application constraints. preserving application-level consistency is primarily the responsibility of the application programmer.

- **Isolation** -> Gurantees that concurrently executing transcation operate without interference, making it appear to each transaction as though no other transcations are running concurrently. this is managed by concurrency control system

- **Durablity** -> ensures that once a transaction completes successfully (commits). its updates persist in the database even in the event of system failures