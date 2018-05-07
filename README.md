# PostgreSQL based Project

Backend Basics with Node.js, PostgreSQL, Sequelize and FeathersJS

## References
[Why We Moved From NoSQL MongoDB to PostgreSQL](https://dzone.com/articles/why-we-moved-from-nosql-mongodb-to-postgresql)

## Sequelize
[documnet](http://docs.sequelizejs.com/manual/installation/usage.html#postgresql)
Sequelize is a promise-based ORM for Node.js v4 and up. It supports the dialects PostgreSQL, MySQL, SQLite and MSSQL and features solid transaction support, relations, read replication and more.

- Sequelize 是一个基于promise 的适用于Node.js 和io.js 的ORM
- Object-Relational Mapping 把关系数据库的表结构映射到对象

## feathersJS
[document](https://docs.feathersjs.com/api/readme.html)
An open source REST and realtime API layer for modern applications.




# Summaries
## Getting Started with Feathers
- In order to make use of these generators, you have to set up the Feathers command line interface first.
```
npm install -g feathers-cli
```
- Once this command is executed, you’ll encounter a number of prompts to help set up your directory. 
```
feathers generate app
```
- After everything is installed, when you open up that directory, you’ll notice a ton of new files and folders. You’ll mostly be working in the src directory. The entry point for your app will be src/index.js.

```
npm start
```
***

## Set up your Postgres Database
- Run your postgres
```
sudo -u postgres psql postgres
```
- Create a new database
```sql
CREATE DATABASE database name;
```
- Create a new table named it message
```sql
dabasename=# CREATE TABLE message(
dabasename(# id SERIAL PRIMARY KEY NOT NULL,
dabasename(# name VARCHAR(50),
dabasename(# message TEXT);
CREATE TABLE

dabasename=# \dt

          List of relations
 Schema |  Name   | Type  |  Owner
--------+---------+-------+----------
 public | message | table | postgres
(1 row)

databasebane=# SELECT * FROM message;
 if | name | message
----+------+---------
(0 rows)

```

***

## Generate a Sequelize Service in Feathers
- Some additional dependencies are needed in order to use Sequelize and Feathers together.
```
npm install --save feathers-sequelize pg pg-hstore
```
- Just like you used a Feathers generator to create the boilerplate of your app, you can also use it to generate services, which, in this case, would be a service for your database. You do this with feathers generate service.
- Choose Sequelize -> PostgreSQL.
```
npm start
```

### Error! No access into the DB
We need to write a connection string that includes any auth information is this: : postgres://USERNAME:PASSWORD@HOST:PORT/DBNAME to access into DB
- Use dotenv to configue it
```
npm install dotenv
```
- Create a .env file
```js
PGNAME=your username
PGPASS=your password
PGPORT=the port for the database
```
- Add config above sequelize file
```js
require('dotenv').config();

const connectionString = `postgres://${process.env.PGNAME}:${process.env.PGPASS}@localhost:${process.env.PGPORT}/postgres`
```

***

## Create a Model
The main one you should focus on is src/models/message.model.js.
- This is where you define the schema. 
- The property text is included by default, but we don’t have any text columns in our table, just name and message.
```js
module.exports = function (app) {
  const sequelizeClient = app.get('sequelizeClient');
  const message = sequelizeClient.define('message', {
    name: {
      type: DataTypes.CHAR,
    },
    message: {
      type: DataTypes.TEXT,
    }
  }, {
    hooks: {
      beforeCount(options) {
        options.raw = true;
      }
    }
  });
```

### GET Request
See how that endpoint was created right away, with no need for writing app.get over and over again for every type of request we want to define? That’s the beauty of Feathers; it automatically generates endpoints and implements all the CRUD operations for you. CRUD operations can be further customized with hooks, but that’s a topic for another tutorial.

### Typo - Change Column name in PostgreSQL
```sql
ALTER TABLE <TableName> 
   ALTER [ COLUMN ] column [ SET DATA ] TYPE data_type [ COLLATE collation ] [ USING expression ]
  RENAME [ COLUMN ] column TO new_column;
```

### createdAt issue
Sequelize can create createdAt and updatedAt for you as long as you don’t initially make any tables in the database.

#### But if we've already created one:

When a model is generated, it looks for certain columns by default, like createdAt and updatedAt, so we need a way to include these columns in our table. There are probably several ways to do this, but the way that I found easiest was to first make createdat and updatedat columns in the database with their defaults set to NOW(), so it will get the current date every time.

```sql
databasename=# ALTER TABLE message
databasename-# ADD COLUMN createdata TIMESTAMP;
ALTER TABLE
databasename=# ALTER TABLE message
databasename-# ADD COLUMN updatedate TIMESTAMP;
ALTER TABLE
databasename=# ALTER TABLE message
databasename-# ALTER COLUMN updatedate
databasename-# SET DEFAULT NOW();
ALTER TABLE
```

```js
 const message = sequelizeClient.define('message', {
    name: {
      type: DataTypes.CHAR,
    },
    message: {
      type: DataTypes.TEXT,
    },
    createdAt: {
      type: DataTypes.DATE,
      field: 'createdat'
    },
    updatedAt: {
      type: DataTypes.DATE,
      field: 'updatedat'
    }
  }, {
    hooks: {
      beforeCount(options) {
        options.raw = true;
      }
    }
  });
```


### PostMan to Send Request to PostgreSQL
- POST: http://localhost:3030/message
```json
{
	"id": 2,
	"name": "Second Message",
	"message": "Hello World! x10"
}
```


***

## Database Notes
As with most scenarios in development, you need to use the right tool that best matches the problem you need to solve.

* Document datastores allow for nested of child relationships

* Columnar datastores allow for good storage and scalability

* Graph databases allow highly related data to be stored and queried efficiently.

* Timeseries databases store temporal data in efficient formats.

* Relational databases use a vector and join heirachy to store data.



***

## Tutorial
[Back-end Basics with Node.js, PostgreSQL, Sequelize, and FeathersJS](https://blog.cloudboost.io/back-end-basics-with-node-js-postgresql-sequelize-and-feathersjs-7ed89b3cd353)


***

## PostgreSQL Query 