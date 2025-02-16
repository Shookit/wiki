Mongo
=====

Remember, "col" means "collection", not "column"!

Basic commands
--------------

```
mongo <dbname> -u <dbuser> -p
show dbs
use <dbname>
show collections

# Removes entire database
db.dropDatabase()

# Removes data in collection (takes optional query)
db.target_collection.remove({})

# Queries
db.target_collection.find().pretty()
db.target_collection.find({'id':'id-89012'}).pretty()

# Update
db.target_collection.update({'id':'id-89012'}, {$set: {'field_to_set': 'newval'}})
```

If collection has a special char, need to use ["prop:erty"] instead of .prop:erty


Nested vs full queries
----------------------

```
# Finds everything where size.uom is 'in'
db.inventory.find( {"size.uom":"in"} )

# Finds every document matching { "size": {"uom": "in" }}
db.inventory.find( {"size":{"uom":"in"}} )
```

Check for unused indices
------------------------

```
db.myColl.aggregate( { $indexStats: { } } )
```


Count by bucket
---------------

For a particular key, create it if it doesn't exist, and increment 'count' on that record by one, atomically

```
primary_key = {
    "timestamp": "2018-01-01",
    "some_id": 1892734978,
}
mongo_ops.append(
    UpdateOne(primary_key, {"$inc": {"count": 1}, "$set": primary_key}, upsert=True)
)
```
