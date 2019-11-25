---
layout: post
title:      "A Beginner's Primer to Relational vs. Non-Relational Databases"
date:       2019-11-24 23:32:38 -0500
permalink:  a_beginners_primer_to_relational_vs_non-relational_databases
---


A quick overview on relational (SQL) and non-relational (NoSQL) databases, what they are, how they differ, and use cases for each.

At a very high level, relational databases like MySQL, PostgreSQL and SQLite3 represent and store data in tables and rows. Non-relational databases like MongoDB represent data in collections of JSON documents. 

**Relational Databases & SQL**

A relational database consists of one or more tables, with columns and rows to organize data. Each column represents a specific type of data, or attribute, and each row is an entry.  The mapping of tables and fields types is call a *schema*. 

The schema must be clearly defined before entries can be made, and is somewhat rigid and difficult to change. It works well with uniform, structured data.

Structured query language - SQL - was initially developed by IBM in the early 70's and is a declarative programming language used by database architects to design relational databases. Once a database is designed, SQL is used to query the database, update, add or delete records. Tables can be interconnected by foreign keys, and queries can cross multiple tables using JOIN statements.

![](https://cdn-8a82.kxcdn.com/wp-content/uploads/2012/01/mysql_vs_mongodb_071717-01-e1500323570310.png)

SQL databases are best used in cases where the data structure is consistent and unchanging. They are also preferred if data integrity and compliance are more important than flexibility or performance.

**NoSQL & Non-relational Databases**

Non-relational databases are *document* oriented, meaning they store data in a single document, which resembles a javascript object. 

![](https://cdn-8a82.kxcdn.com/wp-content/uploads/2012/01/mysql_vs_mongodb_071717-02-1.png)

Non-relational databases offer greater flexibility than their relational counterparts because they don't require advance knowledge of the structure or type of data, and don't require records to be uniform in content. 

This type of database works well when there is large amount of unstructured data, or if the needs aren't clear at the beginning of a project, or if creating a clearly defined schema proves overly complex. 


