_Please note: MongoDB Notes still in progress_
***

## MongoDB Overview
MongoDB is a document oriented database, written in C++
- [Tutorial](https://www.tutorialspoint.com/mongodb/mongodb_overview.htm)
- MongoDB is a NoSQL database program
- each MongoDB server typically has multiple databases
- **Collection** equivalent to a table. Has no enforced schema
- **Document** set of key-value pairs, analogous to a row
Mongo is good for storing data structured as JSON. It's normal to combine data within documents, instead of separating it out into separate tables/collections and joining it.

### Mongo Schema Design
- https://www.mongodb.com/blog/post/6-rules-of-thumb-for-mongodb-schema-design-part-1
Since it's not typical to join collections (like joining tables using SQL), we have a few options
1. **modeling one-to-few**
  This is the case where you can just nest an array of objects within the top-level document. A good example would be users and addresses. Users will probably have one, maybe two addresses, so you can just include that information in the user document.
  - Advantage: You don't have to perform a separate query for details
  - Disadvantage: There's no good way to access the embedded details as standalone entities
2. **modeling one-to-many (or few to many)**
  This is the case where you'd want to use separate collections for each entity. In one of the collections, you'd have an array containing IDs of pointing to the other collection.
- Example: products-to-replacement-parts. Each product would have an array of replacement parts.
- disadvantage: You'd have to do a join. In Mongo, it's called an *application-level join*
- advantage: It would be easy to search on replacement parts, since each one has its own document.
- ALSO: This can be an N-to-N kind of relationship. In this example, products could share replacement parts. This eliminates the need for a join table.
  
3. **modeling one-to-LOTS** in this case, you'd just include a unique identifier in one of the collections. For example, if you have a collection of machines, and another collection of event logs, you'd make the event logs collection point at the machines collection and use an application level join

### mongod
Mongod is the MongoDB daemon. If you want to talk to a Mongo database, you need to connect to the mongod instance on that database.
- handles data requests
- manages data operations
- performs background ops

### mongo-express
Web based Mongo interface

To use mongo express to connect to a container running mongoDB:
`docker run --link hackathon-starter_mongo_1:mongo --net hackathon-starter_default -p 8081:8081 mongo-express`

### Mongo Shell
https://docs.mongodb.com/manual/reference/mongo-shell/
use the `mongo listDatabases` command to use the mongo shell
- `show dbs` print out all databases on a server
- `use admin` (where *admin* is the name of the database) Switches the shell to use the admin database
- `db` prints out current database
- `show collection` display all collections in current database
- `db.users.find()` (where *users* is a collection) display all documents in the collection *users*

## MongoDB and Docker
- [official MongoDB Docker Image](https://hub.docker.com/_/mongo/)
- [official Mongo-Express Docker Image](https://hub.docker.com/_/mongo-express)

MongoDB starting : pid=1 port=27017 dbpath=/data/db 64-bit host=8e3a12832b0f

docker run --link hackathon-starter_mongo_1:mongo --net hackathon-starter_default -p 8081:8081 mongo-express