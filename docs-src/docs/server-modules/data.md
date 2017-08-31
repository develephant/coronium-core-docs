# Overview

The data module provides a simple interface to the underlying Mongo datastore. It is meant for basic usage, see the [Mongo](#server-modules/mongo) module if you require more advanced usage.

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

__Example__

```lua
local obj, err = users:get("1234")
```

---

## save

```lua
<source>:save(obj)
```

__Example__

```lua
local success, err = users:save(obj)
```

---

## delete

```lua
<source>:delete(obj)
```

__Example__

```lua
local success, err = users:delete(obj)
```

---

# Pagination

---

## getPage

```lua
<source>:getPage(query, pageNum, maxPerPage, sortDir)
```

__Example__

```lua
local objects = users:getPage({}, 1, 10)
```

---

