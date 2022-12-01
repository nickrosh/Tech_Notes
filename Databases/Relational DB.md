

The classic SQL database is ubiquitous. Data is stored in tables, divided by columns with set data types, and the data itself is in fields. This defined schema provides great validation and consistency, but doesn't allow for the ease of custom data like in a [Document DB](Document%20DB.md).


### SQL
The Query language for databases, it's a declarative language. The 3 most important command are the following, for **querying data**:

`SELECT` => which columns/computations
`FROM` => the source of the data
`WHERE`=> a filter for the data

Queries can also be saved as **Views**.

You define the Schema with **DDL** (Data Definition Language) commands. Note that any changes to the schema must be tracked and taken care of with migration tooling. Some of the most important commands are:

`CREATE` => create table, index, function, views, etc. Any object
`DROP` => delete an object
`ALTER` => alter the structure of an object

Manipulating data within objects of the database are done with **DML** (Data Manipulation Language) commands, and this includes most of the SQL statements. Some commands are:

`INSERT` => insert data into a table
`UPDATE` => update existing data in a table
`DELETE` => delete records from a table


If we try to map the [CRUD](CRUD.md) operations to SQL, it would be:

Create = `INSERT`
Read = `SELECT`
Update = `UPDATE`
Delete = `DELETE`


### Joins
A key part of SQL and the relational model is the joining of tables and data on certain conditions. Usually one would use `LEFT JOIN` from the central table and a secondary source of data.

![](Pasted%20image%2020220415004100.png)


### Keys
Every table has a primary key which allows you to find each row. The primary key must be unique for every row. Additionally you can designate the combination of two columns as the primary key. Foreign keys are what attach one data field in one table to the data in another. This allows for one-to-many relationships and other [Data Modeling](Data%20Modeling.md).


### Indexes
indexes can be added to columns that you want to search on. Searching for a field in a column without an index requires you to go through the underlying data structure and do a full table scan through disk, this is very slow. An index is a secondary data structure that is loaded into memory to allow for very fast access and search of your desired field(s). They come in two flavors

- **Clustered Index**: the DB rows are sorted on disk in the same order as there index, therefore there can only be one clustered index, but the performance is better than non-clustered.

- **Non-Clustered Index**: there is a secondary data structure that has pointers to the physical rows.  You can have many non-clustered indexes, but each new one increases row write time.