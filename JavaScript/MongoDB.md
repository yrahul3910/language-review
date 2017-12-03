These notes are based on `learnyoumongo`. Use `npm install learnyoumongo` to install.

# Starting Off
MongoDB is a scalable, open source, NoSQL database, which means it's non-relational and queries are made using JavaScript instead of SQL. In Mongo, a collection is equivalent to a table in a SQL database, and a document is equivalent to a record in a table of a SQL DB.  

Use `mongod --version` to check version, and
```
mongod --port port_number --dbpath=./data
```
to start MongoDB. On Cloud9, also use the `--nojournal` option.

## Parameters
`--dbpath=./data` is used since MongoDB defaults to /var/db  
`--nojournal` is used because MongoDB pre-allocated a 2 GB journal file.

# Finding and Filtering Documents
The following code finds all documents in the database where the `age` is greater than the first argument passed to the code.
```
const mongo = require('mongodb').MongoClient;

const url = 'mongodb://localhost:27017/learnyoumongo';
mongo.connect(url, (err, db) => {
	if (err) throw err;
	
	const parrots = db.collection('parrots');
	parrots.find({
		age: {
			$gt: parseInt(process.argv[2])
		}
	}).toArray((e, docs) => {
		if (e) throw e;
		
		console.log(docs);
		db.close();
	});
});
```

We can further filter elements by chaining these `find` expressions. Suppose, in the previous example, we also wanted to print only the `name` and `age` attributes, but not the `_id` attribute. We modiy the code as below:

```
parrots.find({
	age: {
		$gt: parseInt(process.argv[2])
	}},
	{
		age: 1,
		name: 1,
		_id: 0
	}
).toArray((e, docs) => {
	// ...
});
```

# Inserting Documents
The `insert()` method of a collection inserts a document into that collection. The example below inserts a document containing the first name and last name, taken from the args, into the docs collection.

```
const coll = db.collection('docs');
const firstName = process.argv[2], lastName = process.argv[3];

const doc = { firstName, lastName };
coll.insert(doc, (err, data) => {
	if (err) throw err;
	
	data.close();
});
```

# Updating Documents
We call `update()` of a collection to update a record. The first argument is an object that identifies the documents (like a `SELECT` in SQL), and the next argument is an object to update. Example:
```
// document: { a: 2, b: 3 }
coll.update({
	a: 2
}, {
	$set: {
		b: 1
	}
}, (err, db) => {});
// document: { a: 2, b: 1 }
```
If we omit `$set`, the document would be replaced by the object represented by the argument instead.

# Removing Documents
This is done by `remove()`. 
```
coll.remove(selectorObject, callback);
```

# Count 
Use the `count()` function in a similar way.
```
coll.count({
		age: {
			$gt: +process.argv[2]
		}
	}, (err, count) => {
		if (err) throw err;
		console.log(count);
		coll.close();
	});
```
The above snippet counts records where `age` is greater than the first argument.

# Aggregation
Aggregation allows you to find the group fields from multiple documents. There are three ways to do this: the aggregation pipeline, the map-reduce function, and single purpose aggregation methods. This topic discusses the pipeline, but the others can be found at the [MongoDB Documentation](https://docs.mongodb.com/manual/aggregation/).  

The following snippet computes the average of the price field in the documents of the `prices` collection:
```
mongo.connect(url, (err, db) => {
	if (err)
		throw err;
	
	const prices = db.collection('prices');
	prices.aggregate([
		{ 
			$match: { 
				size: process.argv[2] 
			} 
		},
		{ 
			$group: {
				_id: "average",
				avg: { 
					$avg: "$price" 
				} 
			} 
		}
	]).toArray((err, results) => {
		if (err) throw err;
		
		if (!results.length)
			throw new Error("No results found.");

		let avg = Number(results[0].avg).toFixed(2);
		console.log(avg);
		db.close();
	});
});
```