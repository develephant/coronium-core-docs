The __data__ module provides a simple interface to the underlying Mongo database. It is meant for basic tasks. See the [Mongo](/server/modules/mongo/) module if you require more advanced usage.

!!! info "Client-Side Data"
    Most data handling can be handled directly on the client-side, without the need to create a server-side api. See the client-side the __[Data](/client/modules/data/)__ module.

## core.data

To perform any of the object methods, you need a reference to the __data__ module.

```lua
core.data(source_name[, db_name])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|source_name|The source name to use for the objects.|_String_|__Y__|
|db_name|Set a specific database. Default: "_app"|_String_|__N__|

__Returns__

A source object, or __nil__ and an error.

__Example__

```lua
local users = core.data("users")
```

### get

Get a data object from the Mongo database.

```lua
<source>:get(idOrQuery)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|idOrQuery|A string id or query table.|_String_ or _Table_|__Y__|

__Example__

```lua
local obj, err = users:get("id1234")
```

!!! tip
    To retrieve multiple data objects, see the __[getPage](#getpage)__ method.

### save

Create a new, or update an existing, data object and save it in the Mongo database.

```lua
<source>:save(obj)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|obj|A data object to save.|_Table_|__Y__|

__Example__

```lua
local id, err = users:save(obj)
```

### delete

Delete a data object from the Mongo database.

```lua
<source>:delete(idOrObj)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|idOrObj|An object id string or data object with ___id__ field.|_String_ or _Table_|__Y__|

__Example__

```lua
local success, err = users:delete(obj)
```

## Pagination

### getPage

Get multiple data objects from the Mongo database based on a specific criteria.

```lua
<source>:getPage(page, perPage, sort, query)
```

__Parameters__

|Name|Description|Default|Type|Required|
|----|-----------|----|-------|--------|
|page|The page number to return.|none|_Number_|__Y__|
|perPage|The number of objects per page.|20|_Number_|__N__|
|sort|Sort constant or sorting table.|core.ASC|_Const_ or _Table_|__N__|
|query|Specialized query table.|{ } (all records)|_Table_|__N__|

__Sort Constants__

|Enum|Description|
|----|-----------|
|core.ASC|Sort in an ascending order.|
|core.DESC|Sort in a descending order.|

__Example__

```lua
local objects = users:getPage({}, 1, 10)
```
