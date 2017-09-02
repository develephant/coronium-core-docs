# Overview

The __data__ module provides a simple interface to the underlying Mongo database. It is meant for basic tasks. See the [Mongo](#server-modules/mongo) module if you require more advanced usage.

You can access this module directly from the client-side using the client __[Data](/client-module/data)__ module.

---

## core.data

To perform any of the object methods, you need a reference to the __data__ module.

```lua
core.data(source_name[, db_name])
```

__Returns__

A source object, or __nil__ and an error.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|source_name|The source name to use for the objects.|_String_|__Y__|
|db_name|Set a specific database. Default: "_app"|_String_|__N__|

__Example__

```lua
local users = core.data("users")
```

---

## get

```lua
<source>:get(queryOrId)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|description|type|required|

__Example__

```lua
local obj, err = users:get("1234")
```

---

## save

```lua
<source>:save(obj)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|description|type|required|

__Example__

```lua
local success, err = users:save(obj)
```

---

## delete

```lua
<source>:delete(obj)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|description|type|required|

__Example__

```lua
local success, err = users:delete(obj)
```

---

# Pagination

---

## getPage

```lua
<source>:getPage(query, page, perPage, sort)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|description|type|required|

__Example__

```lua
local objects = users:getPage({}, 1, 10)
```

---

