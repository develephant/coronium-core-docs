# Overview

The __DB__ module provides simple access to a document style (schema-less) datastore.

!!! note
    While the __DB__ module uses Mongo, it is a simple abstraction layer providing basic functionality. It is not a full Mongo client. For complex query needs it is recommended that you use the __[MySQL](server-api/mysql/)__ module.

# Database


## new

```lua
local db, err = core.db.new() 
```

## use

```lua
db:use("<database-name>")
```

## close

```lua
db:close()
```

---

# Collections

## collection

```lua
local col = db:collection("<collection-name>")
```

## insertOne

```lua
local id, errOrNum = col:insertOne(doc)
```

## insert

```lua
local ids, errOrNum = col:insert(arr_of_docs)
```

## save

```lua
local id = col:save(doc)
```

## update

```lua
local num, err = col:update(query_or_id, update_doc, flags)
```

## remove

```lua
local num, err = col:remove(query_id, is_single)
```

## findOne

```lua
local doc, err = col:findOne(query_or_id, fields)
```

## find

```lua
local cursor = col:find(query_or_id, fields)
```

## findAndModify

```lua
local doc, err = col:findAndModify(query_or_id, opts)
```

## update

```lua
local num, err = col:update(query_or_id, update_tbl, flags)
```

## rename

```lua
local new_col, err = col:rename(new_name, drop)
```

## ensureIndex

## dropIndex

## create

```lua
local new_col, err = col:create(name) 
```

## aggregate

```lua

```

---

# Cursor

## sort

```lua
local cur = cur:sort(sort_tbl)
```

## skip

```lua
local cur = cur:skip(num)
```

## limit

```lua
local cur = cur:limit(num)
```

## next

```lua
local doc, err = cur:next()
```

## rewind

```lua
local cur = cur:rewind()
```

## all

```lua
local docs, err = cur:all()
```

## count

```lua
local cnt = cur:count()
```

## explain

## distinct

```lua
local doc, err = cur:distinct(key)
```
