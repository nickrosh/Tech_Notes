

In the interest of optimizing the space requirements, as well as logically and optimally arranging our data, we model data in a [Relational Databases](Relational%20Databases.md).


### Database Normalization

There are three general forms of normalization, and a lot of specific rules about it, but it all boils down to a couple key points

- Do not replicate strings
- Use integers for keys

Following these rules, we then set out on splitting our data into the many tables and creating keys that join them

![](../Attachments/Pasted%20image%2020220415010433.png)


### Many-to-One

In many cases, we will want to show many-to-one relationships, like an author and all the books they've written. If we wanted to put it all in one table, there would a huge amount of repeated data; for every entry of a book, the entire author information would be repeated. Thus we use **Foreign Keys** to tie the data together

![](../Attachments/Pasted%20image%2020220415005708.png)


### Many-to-Many

Say we want to model how a book can have many authors, but also each of those authors could have many books. This is not actually possible to be modelled, so instead the database creates a hidden junction table that connects two many-to-one relationships. This is functionally the same as a many-to-many relationship.

![](../Attachments/Pasted%20image%2020220415010015.png)