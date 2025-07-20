---
layout: post
title: "Setting up Databases with PostgreSQL, PSequel, and Python"
date: 2018-04-23 10:00:00 +0400
categories: [data-science, data-engineering]
tags: [sql, postgresql, database, python, pandas, big-data, data-engineering, psycopg2]
original_url: "http://medium.com/data-science/leveraging-python-with-large-databases-pandas-postgresql-5073825167e0"
canonical_url: "http://medium.com/data-science/leveraging-python-with-large-databases-pandas-postgresql-5073825167e0"
description: "A comprehensive guide to setting up and working with PostgreSQL databases for data science, covering installation, basic SQL operations, and Python integration using psycopg2 for handling large datasets."
image: "/assets/images/posts/postgresql-python.jpg"
republished: true
---

> *Originally published on [Medium]({{ page.original_url }}) on {{ page.date | date: "%B %d, %Y" }}*

![Database streaming and processing](https://unsplash.com/photos/JKUTrJ4vK00/download?force=true&w=800)
*Working with large databases requires proper tools and techniques*

As the demand for Data Scientists [continues to increase](https://edgylabs.com/data-scientist-is-americas-best-career-for-the-third-year-running), and is being dubbed the "sexiest job of the 21st century" by various outlets (including [Harvard Business Review](https://hbr.org/2012/10/data-scientist-the-sexiest-job-of-the-21st-century)), questions have been asked of what skills should aspiring data scientists master on their way to their first data analyst job.

There is now a plethora of online courses to gain the skills needed for a data scientist to be good at their job (excellent reviews of online resources [here](https://medium.com/personal-growth/16-top-rated-data-science-courses-e5e161b937c3) and [here](https://medium.freecodecamp.org/a-path-for-you-to-learn-analytics-and-data-skills-bd48ccde7325)). However, as I reviewed the various courses myself, I noticed that a lot of focus is put on exciting and flashy topics like Machine Learning and Deep Learning without covering the basics of what is needed to gather and store datasets needed for such analysis.

## (Big) Data Science

Before we go into PostgreSQL, I suspect many of you have the same question: why should I care about SQL?

Although database management may seem like a boring topic for aspiring data scientists — implementing a dog breed classifier is very rewarding I know! — it is a necessary skill once you join the industry and the data supports this: SQL remains the most common and [in-demand skill](https://www.kdnuggets.com/2016/02/data-science-skills-2016.html) listed in LinkedIn job postings for data science jobs.

![SQL and Big Data](https://unsplash.com/photos/wX2L8L-fGeA/download?force=true&w=800)
*SQL is a necessary skill in many data science applications with large datasets*

[Pandas](https://pandas.pydata.org/) can perform the most common SQL operations well but is not suitable for large databases — its main limit comes to the amount of data one can fit in memory. Hence, if a data scientist is working with large databases, SQL is used to transform the data into something manageable for pandas before loading it in memory.

Furthermore, SQL is much more than just a method to have flat files dropped into a table. The power of SQL comes in the way that it allows users to have a set of tables that "relate" to one another, this is often represented in an "Entity Relationship Diagram" or ERD.

Many data scientists use both simultaneously — they use SQL queries to join, slice and load data into memory; then they do the bulk of the data analysis in Python using [pandas](https://pandas.pydata.org/) library functions.

![Entity Relationship Diagram](https://unsplash.com/photos/xbEVM6oJ1Fs/download?force=true&w=800)
*Example of an ERD showing relationships between database tables*

This is particularly important when dealing with the large datasets found in Big Data applications. Such applications would have tens of TBs in databases with several billion rows.

Data Scientists often start with SQL queries that would extract 1% of data needed to a csv file, before moving to Python Pandas for data analysis.

## Enter PostgreSQL

There is a way to learn SQL without leaving the much loved Python environment in which so much Machine Learning and Deep Learning techniques are taught and used: PostgreSQL.

[PostgreSQL](https://www.postgresql.org/) allows you to leverage the amazing [Pandas library](https://pandas.pydata.org/) for data wrangling when dealing with large datasets that are not stored in flat files but rather in databases.

PostgreSQL can be installed in Windows, Mac, and Linux environments (see [install details here](https://www.tutorialspoint.com/postgresql/postgresql_environment.htm)). If you have a Mac, I'd highly recommend installing the [Postgres.App](http://postgresapp.com/) SQL environment. For Windows, check out [BigSQL](https://www.openscg.com/bigsql/postgresql/installers.jsp/).

PostgreSQL uses a client/server model. This involves two running processes:

• **Server process**: manages database files, accepts connections to the database from client applications, and performs database actions on behalf of the clients.
• **User Client App**: often involves SQL command entries, a friendly graphical interface, and some database maintenance tool.

In real-case scenarios, the client and the server will often be on different hosts and they would communicate over a TCP/IP network connection.

## Installing Postgres.App and PSequel

I'll be focusing on Postgres.App for Mac OS in the rest of the tutorial. After installing [PostgresApp](https://postgresapp.com/), you can setup your first database by following the instructions below:

• Click "Initialize" to create a new server
• An optional step is to configure a `$PATH` to be able to use the command line tools delivered with Postgres.app by executing the following command in Terminal and then close & reopen the window:

```bash
sudo mkdir -p /etc/paths.d && echo /Applications/Postgres.app/Contents/Versions/latest/bin | sudo tee /etc/paths.d/postgresapp
```

You now have a PostgreSQL server running on your Mac with default settings:
`Host: localhost, Port: 5432, Connection URL: postgresql://localhost`

The PostgreSQL GUI client we'll use in this tutorial is [PSequel](http://www.psequel.com/). It has a minimalist and easy to use interface that I really enjoy to easily perform PostgreSQL tasks.

![PSequel Interface](https://unsplash.com/photos/mcSDtbWXUZU/download?force=true&w=800)
*Graphical SQL Client of choice: PSequel provides a clean, intuitive interface*

## Creating Your First Database

Once Postgres.App and PSequel installed, you are now ready to set up your first database! First, start by opening Postgres.App and you'll see a little elephant icon appear in the top menu.

You'll also notice a button that allows you to "Open psql". This will open a command line that will allow you to enter commands. This is mostly used to create databases, which we will create using the following command:

```sql
CREATE DATABASE sample_db;
```

Then, we connect to the database we just created using PSequel. We'll open PSequel and enter the database name, in our case: `sample_db`. Click on "Connect" to connect to the database.

## Creating and Populating Your First Table

Let's create a table (consisting of rows and columns) in PSequel. We define the table's name, and the name and type of each column.

The available datatypes in PostgreSQL for the columns (i.e. variables), can be found on the PostgreSQL [Datatypes Documentation](https://www.postgresql.org/docs/10/static/datatype.html).

In this tutorial, we'll create a simple table with world countries. The first column will provide each country an 'id' integer, and the second column will provide the country's name using variable length character string (with 255 characters max).

```sql
CREATE TABLE country_list (
    id INTEGER,
    name VARCHAR(255)
);
```

Once ready, click "Run Query". The table will then be created in the database. Don't forget to click the "Refresh" icon (bottom right) to see the table listed.

We are now ready to populate our columns with data. There are many different ways to populate a table in a database. To enter data manually, the `INSERT` will come in handy. For instance, to enter the country Morocco with id number 1, and Australia with id number 2, the SQL command is:

```sql
INSERT INTO country_list (id, name) VALUES (1, 'Morocco');
INSERT INTO country_list (id, name) VALUES (2, 'Australia');
```

In practice, populating the tables in the database manually is not feasible. It is likely that the data of interest is stored in CSV files. To import a CSV file into the `country_list_csv` table, you use `COPY` statement as follows:

```sql
COPY country_list_csv(id,name)
FROM 'C:\{path}\{file_name}.csv' 
DELIMITER ',' CSV HEADER;
```

As you can see in the commands above, the table with column names is specified after the `COPY` command. The columns must be ordered in the same fashion as in the CSV file. The CSV file path is specified after the `FROM` keyword. The CSV `DELIMITER` must also be specified.

If the CSV file contains a header line with column names, it is indicated with the `HEADER` keyword so that PostgreSQL ignores the first line when importing the data from the CSV file.

## Common SQL Commands

The key to SQL is understanding statements. A few statements include:

1. **CREATE TABLE** is a statement that creates a new table in a database.
2. **DROP TABLE** is a statement that removes a table in a database.
3. **SELECT** allows you to read data and display it.

SELECT is where you tell the query what columns you want back. FROM is where you tell the query what table you are querying from. Notice the columns need to exist in this table. For example, let's say we have a table of orders with several columns but we are only interested in a subset of three:

```sql
SELECT id, account_id, occurred_at
FROM orders;
```

Also, the **LIMIT** statement is useful when you want to see just the first few rows of a table. This can be much faster for loading than if we load the entire dataset. The **ORDER BY** statement allows us to order our table by any row. We can use these two commands together to a table of 'orders' in a database as:

```sql
SELECT id, account_id, total_amt_usd
FROM orders
ORDER BY total_amt_usd DESC
LIMIT 5;
```

## Explore Other SQL Commands

Now that you know how to setup a database, create a table and populate in PostgreSQL, you can explore the other common SQL commands as explained in the following tutorials:

• [SQL Basics for Data Science](https://towardsdatascience.com/how-to-ace-data-science-interviews-sql-b71de212e433)
• [Khan Academy: Intro to SQL](https://www.khanacademy.org/computing/computer-programming/sql/sql-basics/p/creating-a-table-and-inserting-data)
• [SQL in a Nutshell](https://towardsdatascience.com/sql-in-a-nutshell-part-1-basic-real-world-scenarios-33a25ba8d220)
• [Cheat Sheet SQL Commands](https://www.codecademy.com/articles/sql-commands)

## Accessing PostgreSQL Database in Python

Once your PostgreSQL database and tables are setup, you can then move to Python to perform any Data Analysis or Wrangling required.

PostgreSQL can be integrated with Python using [psycopg2 module](http://initd.org/psycopg/docs/). It is a popular PostgreSQL database adapter for Python. It is shipped along with default libraries in Python version 2.5.x onwards.

Connecting to an existing PostgreSQL database can be achieved with:

```python
import psycopg2

conn = psycopg2.connect(
    database="sample_db", 
    user="postgres", 
    password="pass123", 
    host="127.0.0.1", 
    port="5432"
)
```

Going back to our country_list table example, inserting records into the table in sample_db can be accomplished in Python with the following commands:

```python
cur = conn.cursor()
cur.execute("INSERT INTO country_list (id, name) VALUES (1, 'Morocco')")
cur.execute("INSERT INTO country_list (id, name) VALUES (2, 'Australia')")
conn.commit()
conn.close()
```

Other commands for creating, populating and querying tables can be found on various tutorials on [Tutorial Points](https://www.tutorialspoint.com/postgresql/postgresql_python.htm) and [PostgreSQL Tutorial](http://www.postgresqltutorial.com/postgresql-python/).

## Conclusion

You now have a working PostgreSQL database server ready for you to populate and play with. It's powerful, flexible, free and is used by numerous applications.

The combination of PostgreSQL's robustness and Python's analytical capabilities provides data scientists with a powerful toolkit for handling large datasets that don't fit comfortably in memory. By mastering these fundamental database skills alongside your machine learning knowledge, you'll be well-equipped to handle real-world data science challenges.

---

*If you found this article helpful, feel free to connect with me on [LinkedIn](https://linkedin.com/in/hamzabendemra){:target="_blank" rel="noopener"} or check out my other articles on [Medium](https://medium.com/@hamzabendemra){:target="_blank" rel="noopener"}.*
