# Overview

The __DB__ module provides simple access to a document style (schema-less) datastore.

!!! note
    While the __DB__ module uses Mongo, it is a simple abstraction layer providing basic functionality. It is not a full Mongo client. For complex query needs it is recommended that you use the __[MySQL](server-api/mysql/)__ module.

# Database

## new

Connect to a database and return a database object. If the database does not exist, it will be created.

```lua
core.db.new( db_name )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to connect to.|_String_|__Y__|

__Example__

```lua
local db, err = core.db.new( "app" ) 
```

---

## use

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
db:use( "app2" )
```

---

## close

Close the database connection.

```lua
db:close()
```

!!! note
    To keep memory usage down, you should always close the database connection when finished.

---

## collection

Select and return a collection to operate on. If the collection does not exist, it will be created.

```lua
db:collection( name )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|The collection name to use.|_String_|__Y__|

```lua
local coll = db:collection( "users" )
```

---

# Collections

The following methods operate on collections. See __[db:collection](#collection)__ above to gain access to a collection object.

## insertOne

Insert a single document into the collection. Returns the newly created __id__, or __nil__ and an error.

```lua
collection:insertOne(doc)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|doc|A data table with the document data.|_Table_|__Y__|

__Example__

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

local id, err = coll:insertOne(doc)
```

---

## insert

Insert multiple documents into a collection. Returns array of __ids__, or __nil__ and an error.

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

local ids, err = coll:insert( docs )
```

---

## findOne

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

---

## find

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


!!! tip
    This method returns a __cursor__ object which is chainable. For example, to return all documents found by the query in one call: `local docs = coll:find( query ):all()`

__See also__

- [Cursor](#cursor)
- [MongoDB to Lua](#mongodb-to-lua)
- [Resources](#resources)

---

## findAndModify

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

---

## update

```lua
collection:update(queryOrId, updateDoc, flags)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|queryOrId|A table based query, or a string based id.|_Table_ or _String_|__Y__|
|updateDoc|Options for the modification (see below).|_Table_|__Y__|
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

---

## save

Updates a document based on the contained id. If no id is present, creates a new document.

```lua
collection:save(doc)
```

!!! note
    This method is the same as setting __upsert=true__ when using the __update__ method.

```lua
local id = coll:save(doc)
```

---

## remove

Remove a document or documents based on the query.

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

---

## create

Create a new collection. Returns a new empty collection, or __nil__ and an error.

```lua
collection:create(name) 
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|The preferred name of the collection.|_String_|__Y__|

__Example__

```lua
local new_col, err = col:create(name) 
```

---

## drop

Remove a collection and all the containing documents.

```lua
local res, err = collection:drop()
```

---

## rename

Rename/copy a collection. Returns new collection, or __nil__ and an error.

```lua
collection:rename(new_name, drop)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|new_name|The preferred name for the collection.|_String_|__Y__|
|drop|Remove the previously named collection|_Bool_|__N__|

__Example__

```lua
local new_col, err = col:rename(new_name, drop)
```

---

## ensureIndex

Create indexes for a collection.

```lua
collection:ensureIndex( fields )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|fields|A table array of field names.|_Table_|__Y__|

__Example__

```lua
coll:ensureIndex({ "name", "age" })
```

---

## dropIndex

Drop indexes for a collection.

```lua
collection:dropIndex( fields )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|fields|A table array of field names.|_Table_|__Y__|

__Example__

```lua
coll:dropIndex({ "name", "age" })
```

---

## aggregate

```lua

```

---

# Cursor

A cursor object contains a group of documents returned from the __[find](#find)__ method. After setting the needed cursor methods, use the __[all](#all)__ cursor method to retrieve the documents. For example:

```lua
-- return first 10 docs from the find request
local docs = cursor:limit(10):all()
```

!!! tip
    Cursor object methods are chainable.

## all

```lua
local docs, err = cur:all()
```

---

## sort

```lua
local cur = cur:sort(sort_tbl)
```

---

## skip

```lua
local cur = cur:skip(num)
```

---

## limit

```lua
local cur = cur:limit(num)
```

---

## count

```lua
local cnt = cur:count()
```

---

## rewind

```lua
local cur = cur:rewind()
```

---

## next

```lua
local doc, err = cur:next()
```

---

## explain

---

## distinct

```lua
local doc, err = cur:distinct(key)
```

---

# MongoDB to Lua

MongoDB used JSON objects. When viewing the documentation on the MongoDB website, you must convert the JSON information to Lua. What follows are some tips to do that.

## Operators

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

## Arrays

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

# Resources

- [Find Examples](https://docs.mongodb.com/v3.0/reference/method/db.collection.find/#examples)
- [Query Operators](https://docs.mongodb.com/v3.0/reference/operator/query/)
- [Update Operators](https://docs.mongodb.com/v3.0/reference/operator/update/)
- [SQL to MongoDB Mapping Chart](https://docs.mongodb.com/v3.0/reference/sql-comparison/)