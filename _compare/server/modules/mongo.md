!!! tldr "Important Note"
    The Mongo module has most, but not all, functionality of the full client. It is recommended that you manage your databases using an external tool. See __[Administration](#administration)__ below.

!!! info "Client-Side Data"
    For simple data manipulation using Mongo, take a look at the __[Data](/server/modules/data/)__ module.

## core.mongo

Connect to a database and return a database object. If the database does not exist, it will be created.

```lua
core.mongo( db_name )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to connect to.|_String_|__Y__|

__Example__

```lua
local db, err = core.mongo( "app" ) 
```

### use

Change the current working database. If the database does not exist, it will be created.

```lua
db:use( db_name )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to connect to.|_String_|__Y__|

__Example__

```lua
local db = db:use( "app2" )
```

### close

Close the database connection.

```lua
db:close()
```

!!! warning "Close Connections"
    To keep memory usage down, you should always close the database connection when finished.

### collection

Select and return a collection to operate on. If the collection does not exist, it will be created.

```lua
db:collection( name )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|The collection name to use.|_String_|__Y__|

__Example__

```lua
local coll = db:collection( "users" )
```

### listCollections

Retreive a list of collections. Returns a __table__ array, or __nil__ and an error.

```lua
db:listCollections()
```

__Parameters__

This method has no parameters.

__Example__

```lua
local list, err = db:listCollections()

for i=1, #list do
  print(list[i]) -- collection name
end
```

## Collections

The following methods operate on collections. See __[db:collection](#collection)__ above to gain access to a collection object.

### save

Inserts or updates a single document. If no id field is present, creates a new document, otherwise updates the document. Returns the document id, or __nil__ and an error. 

```lua
collection:save(doc)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|doc|A data table with the document data.|_Table_|__Y__|

__Example__

_Insert a new document_

```lua
local doc = {
  name = "Jimmy",
  age = 20,
  active = true,
  colors = {
    "red",
    "green",
    "blue"
  }
}

local id, err = coll:save(doc)
```

_Update an existing document_

```lua
local doc = coll:findOne(<queryOrId>)

doc.name = "Nancy"

local id, err = coll:save(doc)
```

!!! note
    This method is the same as setting __upsert=true__ when using the __update__ method.

### insert

Insert multiple documents into a collection. Returns array of __ids__ and __number__ inserted, or __nil__ and an error.

```lua
collection:insert( docs )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|docs|A table array of data tables with the document data.|_Table_|__Y__|


__Example__

```lua
local docs = {
  {
    name = "Tina",
    age = 24
  },
  {
    name = "Jeff",
    age = 36
  }
}

local ids, errOrNum = coll:insert( docs )
```

### findOne

Find and return the first document that matches the query or id. Returns the __doc__, or __nil__ and an error.

```lua
collection:findOne(queryOrId, fields)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|queryOrId|A table based query, or a string based id.|_Table_ or _String_|__Y__|
|fields|Limit the fields returned from the document. Returns all fields by default.|_Table_|__N__|

__Example__

_By query_

```lua
local doc, err = coll:findOne( {name="Jeff", age=36} )
```

_By id_

```lua
local doc, err = coll:findOne( "59a583516362392a28000001" )
```

_Limit fields_

```lua
-- return only the 'name' field
local doc, err = coll:findOne( {name="Jeff", age=36}, {name=true} )

-- return all except the 'name' field
local doc, err = coll:findOne( {name="Jeff", age=36}, {name=false} )
```

__See also__

- [MongoDB to Lua](#mongodb-to-lua)

### find

Find multiple documents based on query. Returns a new __[cursor](#cursor)__ object.

```lua
collection:find(query, fields)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|queryOrId|A table based query, or a string based id.|_Table_ or _String_|__Y__|
|fields|Limit the fields returned from the document. Returns all fields by default.|_Table_|__N__|

__Example__

```lua
-- find documents where name equals "Jimmy"
local cursor = coll:find({ name = "Jimmy" })

-- find documents where age greater than 20
local cursor = coll:find({ age = {["$gt"] = 20} })

-- find documents where age greater than 20, and name is "Tina"
local cursor = coll:find({ age = {["$gt"] = 20}, name = "Tina" })
```


!!! tldr "Chainable Cursors"
    This method returns a __cursor__ object which is chainable. For example, to return all documents found by the query in one call: `local docs = coll:find( query ):all()`

__See also__

- [Cursor](#cursor)
- [MongoDB to Lua](#mongodb-to-lua)
- [Resources](#resources)

### findAndModify

Finds the first document that matches the query and updates it in place based on the __options__. Returns old doc (unless __new__ option is set), or __nil__, and an error.

```lua
collection:findAndModify(queryOrId, options)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|queryOrId|A table based query, or a string based id.|_Table_ or _String_|__Y__|
|options|Options for the modification (see below).|_Table_|__Y__|

__Options Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|update|Description|_Table_|__Y__|
|new|Description|_Bool_|__N__|
|sort|Description|_Table_|__N__|
|fields|Description|_Table_|__N__|

```lua
local doc, err = col:findAndModify(query_or_id, opts)
```

### update

Update a record. Returns __number__ updated, or __nil__ and an error.

```lua
collection:update(queryOrId, updateDoc, flags)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|queryOrId|A table based query, or a string based id.|_Table_ or _String_|__Y__|
|updateDoc|Options for the modification (see examples).|_Table_|__Y__|
|flags|Flags for the update (see below).|_Table_|__Y__|

__Flags Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|upsert|Description|_Bool_|Required|
|multi|Description|_Bool_|Required|

__Example__

_Replace entire document_

```lua
local num, err = collection:update({name="Jim"}, {
  name="John", 
  age=45
})
```

_Update and add fields_

```lua
local num, err = collection:update({name="John", age=45}, {
  ["$set"] = { age = 43, active = true }
})
```

_Using id_

```lua
local num, err = collection:update("59a583516362392a28000001", {
  ["$set"] = { active = false }
})
```

_Update multiple_

```lua
local num, err = collection:update({active = false}, 
  { ["$set"] = { active = true } }, 
  { multi = true }
)
```

_Add field to documents if missing_

```lua
local num, err = collection:update({ active = {["$exists"] = false }}, 
  { ["$set"] = { active = true } }, 
  { multi = true }
)
```

__See also__

- [MongoDB to Lua](#mongodb-to-lua)
- [Resources](#resources)

### remove

Remove a document or documents based on the query. Returns __number__ of records removed, or __nil__ and an error.

```lua
collection:remove(queryOrId, isSingle)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|queryOrId|A table based query, or a string based id.|_Table_ or _String_|__Y__|
|isSingle|Remove only the first returned document when the query matches multiple documents.|_Bool_|__N__|

```lua
local num, err = coll:remove(query_id, is_single)
```

### drop

Remove a collection and all the containing documents. Returns __true__, or __nil__ and an error.

```lua
collection:drop()
```

__Parameters__

This method has no parameters.

__Example__

```lua
local res, err = coll:drop()
```

### rename

Rename a collection. Returns newly named collection, or __nil__ and an error.

```lua
collection:rename(new_name, drop)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|new_name|The preferred name for the collection.|_String_|__Y__|
|drop|Clear all documents before renaming. Default: false|_Bool_|__N__|

__Example__

```lua
local new_col, err = col:rename(new_name, drop)
```

__Moving to another database__

To rename and move the collection to another database, pass the full namespace to the __new_name__ parameter:

```lua
-- namespace <db>.<collection>
col:rename("otherdb.newname")
```

### getIndexes

The current collection indexes. Returns a __table__ array with index objects, or __nil__ and an error.

```lua
collection:getIndexes()
```

__Parameters__

This method has no parameters.

__Index Object Keys__

|Name|Description|Type|
|----|-----------|----|
|key|The index key info.|_Table_|
|name|The index name.|_String_|

__Example__

```lua
local indexes, err = coll:getIndexes()

for i=1, #indexes do
  local index = indexes[i]
  --index key info
  for field, opt in pairs(index.key) do
    print(field, opt)
  end
  --index name
  print(index.name)
end
```

### createIndex

Create index(es) for a collection. Returns __true__, or __nil__ and an error.

```lua
collection:createIndex(index_arr)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|index_arr|A table array of index objects.|_Table_|__Y__|

__Example__

_Simple index_

```lua
local res, err = coll:createIndex({
  { key = { age = 1 } }
})
```

_Compound index_

```lua
local res, err = coll:createIndex({
  { key = { age = 1, name = -1 } }
})
```

_Create multiple indexes_

```lua
local res, err = coll:createIndex({
  { key = { name = 1 } },
  { key = { age = -1, score = 1 } }
})
```

__Index names__

By default Mongo will generate the index name based on the fields passed. To set an index name manually, pass a __name__ key in the object.

```lua
local res, err = coll:createIndex({
  { key = { age = 1 }, name = "age_asc" }
})
```

### dropIndex

Drop indexes for a collection. Returns __true__, or __nil__ and an error.

```lua
collection:dropIndex( name )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|index_name|The index name to drop.|_String_|__Y__|

__Example__

```lua
local res, err = coll:dropIndex( "age_asc" )
```

__See also__

- [getIndexes](#getindexes)

### aggregate

Performs aggregation operation based on the pipeline commands. By default returns a cursor, or __nil__ and an error.

```lua
collection:aggregate(pipeline)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|pipeline|The aggregation pipeline commands.|_Table_|__Y__|

__Example__

```lua
local cur, err = users:aggregate({
  { ["$project"] = { name = { ["$toUpper"] = "$name" }, _id = 0 } },
  { ["$sort"] = { name = 1 } }
})
```

_Output aggregation results to a collection instead of a cursor_

!!! warning "Important"
    The __$out__ key must be the last step in the pipeline. Creates and returns a new collection.

```lua
local coll, err = users:aggregate({
  { ["$project"] = { name = { ["$toUpper"] = "$name" }, _id = 0 } },
  { ["$sort"] = { name = 1 } },
  { ["$out"] = "uppernames" }
})
```

__See also__

- [Resources](#resources)

## Cursor

A cursor object contains a group of documents returned from the __[find](#find)__ method. After setting the needed cursor methods, use the __[all](#all)__ or __[next](#next)__ cursor method to retrieve the documents. For example, using __all__:

```lua
-- return first 10 docs from the find request
local docs = cursor:limit(10):all()
```

!!! tip
    Cursor object methods are chainable.

### all

Return document(s) based on the previous cursor options. Returns a __table__ array of documents, or __nil__ and an error.

```lua
cursor:all()
```

!!! warning "Important"
    Always call this method last (or in a chain), to retrieve the documents from the cursor.

__Parameters__

This method has no parameters.

__Example__

```lua
local docs, err = cur:all()
```

### sort

Sort the documents currently held in the cursor based on the sorting table. Returns the cursor.

```lua
cursor:sort( sort_tbl )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|sort_tbl|A table with field based sorting options.|_Table_|__Y__|

__Example__

```lua
--sort by age, ascending
local cur = cur:sort({ age = core.ASC })

--sort by age descending and name ascending
local cur = cur:sort({age = core.DESC, name = core.ASC })
```

### skip

Skip a specific amount of documents in the cursor results. Returns the cursor.

```lua
cursor:skip(num)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|num|The amount of records to skip.|_Number_|__Y__|

__Example__

```lua
--skip the first 20 documents
local cur = cur:skip( 20 )
```

### limit

Limit the amount of documents returned based on the current cursor options. Returns the cursor.

```lua
cursor:limit(num)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|num|The records limit amount.|_Number_|__Y__|

__Example__

```lua
--return the first 10 documents
local cur = cur:limit( 10 )
```

### count

Return the current document amount based on the current cursor options. Returns a __number__.

```lua
cursor:count()
```

__Parameters__

This method has no parameters.

__Example__

```lua
local cnt = cur:count()
```

### rewind

Set the cursor pointer back to the begining of the document results. Returns the cursor.

```lua
cursor:rewind()
```

__Parameters__

This method has no parameters.

__Example__

```lua
local cur = cur:rewind()
```

### next

Interate over the cursor results. On each call, returns a document, or __nil__ and an error.

```lua
cursor:next()
```

__Parameters__

This method has no parameters.

__Example__

```lua
while true do
  local doc = cur:next()
  if doc == nil then break end
end
```

__See also__

- [all](#all)
- [rewind](#rewind)

### distinct

Pull distinct key values from the cursor results. Returns a __table__ array, or __nil__ and an error.

```lua
cursor:distinct(key)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|key|The field key to operate on.|_String_|__Y__|

__Example__

```lua
local res, err = cur:distinct( "age" )
-- 'res' is a table array
```

## MongoDB to Lua

MongoDB used JSON objects. When viewing the documentation on the MongoDB website, you must convert the JSON information to Lua. What follows are some tips to do that.

### Operators

MongoDB uses special operators that are prefixed with a dollar sign ( $ ). These operators keys must be set using an array style syntax when providing them using Lua.

|MongoDB Key|Lua Key|
|-----------|-------|
|$inc|["$inc"]|
|$set|["$set"]|

__Example__

```lua
coll:update({name="Jimmy"}, {
  ["$set"] = {
    age = 23
  }
})
```

### Arrays

Arrays are rendered as indexed tables.

|MongoDB Array|Lua Array|
|-------------|---------|
|["red", 23, true]|{"red", 23, true}|

__Example__

```lua
coll:insert({
  name = "Tina",
  colors = {"red","green","blue"}
})
```

#### Append values

_Append a single value_

```lua
coll:update(<queryOrId>, {
  ["$push"] = {
    colors = "yellow"
  }
})
```

_Append multiple values_

```lua
coll:update(<queryOrId>, {
  ["$push"] = {
    colors = { ["$each"] = { "brown", "purple" } }
  }
})
```

#### Remove values

_Remove first element_

```lua
coll:update(<queryOrId>, {
  ["$pop"] = {
    colors = -1
  }
})
```

_Remove last element_

```lua
coll:update(<queryOrId>, {
  ["$pop"] = {
    colors = 1
  }
})
```

## Resources

- [Find Examples](https://docs.mongodb.com/v3.0/reference/method/db.collection.find/#examples)
- [Query Operators](https://docs.mongodb.com/v3.0/reference/operator/query/)
- [Update Operators](https://docs.mongodb.com/v3.0/reference/operator/update/)
- [Array Update Operators](https://docs.mongodb.com/v3.0/reference/operator/update-array/)
- [Aggregation Operators](https://docs.mongodb.com/v3.0/reference/operator/aggregation/)
- [SQL to MongoDB Mapping Chart](https://docs.mongodb.com/v3.0/reference/sql-comparison/)

## Administration

See the Webmin __[databases](/server/webmin/databases/)__ section to learn how to administer your MongoDB database.