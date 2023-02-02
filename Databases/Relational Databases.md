
The classic SQL database is ubiquitous. Data is stored in tables, divided by columns with set data types, and the data itself is in fields. This defined schema provides great validation and consistency, but doesn't allow for the ease of custom data like in a [Document Databases](Document%20Databases.md). RDBs are characterized by its organization into tables

## RDB Organization

#### Keys

Every table has a primary key which allows you to find each row. The *primary key* must be unique for every row. Additionally you can designate the combination of two columns as the primary key. *Foreign keys* are what attach one data field in one table to the data in another. This allows for one-to-many relationships and other [Data Modeling](Data%20Modeling.md).

#### Normalization

The most important component of RDBs is the relationship of data, and how you are able to create relationships among disparate data fields. The process of removing repeated data and putting into separate tables is called *Normalization*

![](../Attachments/Pasted%20image%2020221228211559.png)

Instead of saving every single data field, simply use Foreign Keys, which are much more space efficient to point to the needed data in the other tables. When you are querying and actually need the data, you can then utilize *Joins* to recombine the data

*Denormalization* is the opposite of this process that is done in rare circumstances. Joins can be expensive, and for tables that have huge read throughput, you can de-normalize the data to contain everything you need without a join. This comes at the cost of using extra space, but will speed up queries considerable depending on the number of joins.

#### Joins
A key part the relational model is the joining of tables and data on certain conditions. Usually one would use `LEFT JOIN` from the central table and a secondary source of data. `INNER JOIN` only returns rows that have intersecting keys

![](../Attachments/Pasted%20image%2020220415004100.png)


#### Indexes
indexes can be added to columns that you want to search on. Searching for a field in a column without an index requires you to go through the underlying data structure and do a full table scan through disk, this is very slow. An index is a secondary data structure that is loaded into memory to allow for very fast access and search of your desired field(s). They come in two flavors

- **Clustered Index**: the DB rows are sorted on disk in the same order as there index, therefore there can only be one clustered index, but the performance is better than non-clustered.

- **Non-Clustered Index**: there is a secondary data structure that has pointers to the physical rows.  You can have many non-clustered indexes, but each new one increases row write time.


## SQL
The Query language for databases, *SQL* is how every query is described. Every single read and write is executed as a SQL statement. The 3 most important *Read* command are the following, for **querying data**:

`SELECT` => which columns/computations
`FROM` => the source of the data
`WHERE`=> a filter for the data

Queries can also be saved as **Views**.

You define the Schema with **DDL** (Data Definition Language) commands. Note that any changes to the schema must be tracked and taken care of with migration tooling. Some of the most important commands are:

`CREATE` => create table, index, function, views, etc. Any object
`DROP` => delete an object
`ALTER` => alter the structure of an object

Manipulating data within objects of the database are done with **DML** (Data Manipulation Language) commands, and this includes most of the SQL statements. These are *Writes*. Some commands are:

`INSERT` => insert data into a table
`UPDATE` => update existing data in a table
`DELETE` => delete records from a table


If we try to map the [CRUD](../Internet%20&%20Networking/REST%20API.md) operations to SQL, it would be:

Create = `INSERT`
Read = `SELECT`
Update = `UPDATE`
Delete = `DELETE`


#### Object Relational Mappers

Rather than writing raw SQL for every read and write, most frameworks and programming languages have *Object Relational Mappers* or ORMs which map native programming objects and language commands to SQL. This has the benefit of speeding up development if the developer does not know SQL, and also automatically providing security against SQL injections. But there are key drawbacks, such as possible inefficient Queries from the auto-generated SQL. There is also the issue of unintentional queries, as edge cases occur where you think you are doing one thing, and the ORM generates SQL which does something slightly different.








