# Overview

Your Coronium Core server contains a local __MySQL__ database. It's also possible to connect to a remote __MySQL__ database as well.

---

## query

```lua
core.mysql.query( connection_tbl, query_str )
```

__Parameters__

|Name|Description|Default|Required|
|----|-----------|-------|--------|
|connection_tbl|The connection table for the database (see below).|_nil_|__Y__|
|query_str|The MySQL query to send to the database.|_nil_|__Y__|

__The Connection Table__

You need to provide a database connection table to select the working database. The table uses the following keys:

|Key|Description|Type|Required|
|----|----------|----|--------|
|database|The database to work with.|_String_|__Y__|
|user|The database user.|_String_|__N__|
|password|The user password|_String_|__N__|
|host|The database host, can be used for external connections.|_String_|__N__|
|port|The database port number.|_Number_|__N__|

__Example__

```lua
local client_id = 100
local conn_tbl = { database = "clients" }
local query = cloud.sf("SELECT * FROM orders WHERE id=%d", client_id)
local ok, result = cloud.mysql.query(conn_tbl, query)
if not ok then
  core.log(result) --error is in the result
end

-- `result` is a table array of records.
```

---

# string

Return a __MySQL__ safe string.

```lua
core.mysql.string( unwashed_string )
```

__Parameters__

|Name|Description|Requried|
|----|-----------|--------|
|unwashed_string|The string to make SQL "safe".|__Y__|

__Example__

```lua
local safe_str = core.mysql.string("Some possibly unsafe string.")
```

---

# Databag Usage

A __databag__ wraps common MySQL actions into a module format.

!!! note
    If you prefer the query style shown above, feel free to use it instead. They both produce similar results.

---

## databag

A prebuilt data object for basic __MySQL__ handling.

```lua
core.mysql.databag( connection_tbl )
```

__Returns__

A new databag instance.

__Parameters__

See __The Connection Table__ section above.

__Example__

```lua
local conn_tbl = { database = "leaderboard" }
local databag = core.mysql.databag( conn_tbl )
```

---

## select

```lua
databag:select( select_tbl )
```

__Parameters__

This method requires a table with the following keys (not all keys required):

__Select Table Keys__

|Key|Description|Type|Required|
|---|-----------|----|--------|
|tableName|Name of the table to operate on.|_String_|__Y__|
|columns|Specific columns to select as strings.|_Table array_|__N__|
|where|Any additional WHERE clause to apply.|_String_|__N__|
|orderby|The sorting attributes. See __Orderby Table__.|_Table_|__N__|
|limit|Limit the amount of the return to this number.|_Number_|__N__|
|distinct|Do not select duplicate column values.|_Boolean_|__N__|

__Orderby Table Keys__

The __orderby__ key in the __Select Table__ is a table array filled with table objects containing the following keys:

|Key|Description|Type|Required|
|---|-----------|----|--------|
|colName|Name of a column to sort on.|_String_|__Y__|
|direction|Which direction to sort the column|__core.ASC__ or __core.DESC__|__N__|

__Returns__

A __result__ or __nil__, __error__, and possible __errorCode__.

__Example__

```lua
-- Select and return all records from 'scores'.
local conn_tbl = { database = "leaderboard" }
local db = core.mysql.databag(conn_tbl)
local result, err = db:select({
  tableName = 'scores'
})

if not result then
  return core.error(err)
end

-- Records are Lua tables in a table array.
for _, record in ipairs(result) do
  core.log(record.player..' scored '..record.score)
end

return result
```

__Example__

```lua
-- Select and return refined query.
local conn_tbl = { database = "leaderboard" }
local db = core.mysql.databag( conn_tbl )

local result, err, errCode = db:select({
  tableName = 'scores',
  columns = {'score'},
  where = 'score > 10',
  limit = 10,
  orderby = {
    score = core.DESCENDING
  }
})

if not result then
  return core.error( err, errCode )
end

return result

```

---

## insert

```lua
databag:insert( insert_tbl )
```

__Parameters__

|Key|Description|Type|Required|
|---|-----------|----|--------|
|insert_tbl|The insert table options (see below).|_Table_|__Y__|

__Insert Table Keys__

|Key|Description|Type|Required|
|---|-----------|----|--------|
|tableName|The MySQL table name to operate on.|_String_|__Y__|
|columns|An array of column names as strings.|_Table array_|__Y__|
|values|An array of values for each specified column.|_Table array (mixed)_|__Y__|

!!! warning
    The amount of columns must match the amount of values for this action to be successful.

__Returns__

A __result__ or __nil__, __error__, and possible __errorCode__.

__Example__

```lua
local conn_tbl = { database = "leaderboard" }
local db = core.mysql.databag( conn_tbl )
local result, err = db:insert({
  tableName = 'scores',
  columns = {'score', 'player'},
  values = {200, 'Sandy'}
})

if not result then
  return core.error(err)
end

return result
```

---

## update

```lua
databag:update( update_tbl )
```

__Parameters__

|Key|Description|Type|Required|
|---|-----------|----|--------|
|update_tbl|The update table options (see below).|_Table_|__Y__|

__Update Table Keys__

|Key|Description|Type|Required|
|---|-----------|----|--------|
|tableName|The name of the table to operate on.|_String_|__Y__|
|values|Table of name = val pairs.|_Table_|__Y__|
|where|Where the colums should be updated.|_String_|__N__|

__Returns__

A __result__ or __nil__, __error__, and possible __errorCode__.

__Example__

```lua
local conn_tbl = { database = "leaderboard" }
local databag = core.mysql.databag( conn_tbl )
local result, err = databag:update({
  tableName = 'scores',
  values = {
    score = 230
  },
  where = 'player='..core.mysql.string(player)
})

if not result then
  return core.error(err)
end

return result
```

---

## delete

```lua
databag:delete( delete_tbl )
```

__Parameters__

|Key|Description|Type|Required|
|---|-----------|----|--------|
|delete_tbl|The delete table options (see below).|_Table_|__Y__|

__Delete Table Keys__

|Key|Description|Type|Required|
|---|-----------|----|--------|
|tableName|The name of the table to operate on.|_String_|__Y__|
|where|A WHERE clause to limit deletions to.|_String_|__N__|
|force|Disables safety check for mising where clause.|_Boolean_|__N__|

!!! warning
    Though optional, you should _always_ include a where key query when removing records. Without it, you could possibly delete _all_ of the tables records.

__Returns__

A __result__ or __nil__, __error__, and possible __errorCode__.

__Example__

```lua
local conn_tbl = { database = "leaderboard" }
local databag = core.mysql.databag( conn_tbl )
local result, err = databag:delete({
  tableName = 'scores',
  where = 'score < 10'
})

if not result then
  return core.error(err)
end

return result
```

---

# cleanbag

Clears all internal databag query variables _except_ the connection table. The databag is still usable.

```lua
databag:cleanbag()
```

!!! note
    You generally don't need to call this method. It's called automatically before any new actions take place.
