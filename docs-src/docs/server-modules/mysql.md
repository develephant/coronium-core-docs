# Overview

The __mysql__ module allows you to run queries against the local MySQL server instance. You can also connect to remote databases.

---

## query | q

```lua
core.mysql.query( db_name, query_str )
--OR
core.mysql.q( db_name, query_str )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database name to issue commands against.|_String_|__Y__|
|query_str|The MySQL query to send to the database.|_String_|__Y__|

!!! note
    You can also connect to remote MySQL databases. See __[Remote Databases](#remote-databases)__ for more info.

__Result Values__

The following outlines the __result__ values returned with each query type.

_SELECT_

A __table__ array of records. If no records are found, returns an empty table.

_INSERT_

A __number__ representing the insertion id.

_UPDATE_

A __number__ indicating the amount of updated rows.

_DELETE_

A __number__ indicating the amount of deleted rows.

!!! note
    Any other commands will be returned as __table__. It is recommended that you manage your databases using an external tool. See __[Administration](#administration)__ below.

---

__Select Example__

```lua
local query = core.sf("SELECT * FROM orders WHERE id=%d", 100)

local result, err = core.mysql.query("clients", query)

if not result then
  core.log(err)
end

local record
for i=1, #result do
  record = result[i] --a record as a table
end

```

---

# string | str

Return a __MySQL__ safe string.

```lua
core.mysql.string( unwashed_string )
--OR
core.mysql.str( unwashed_string )
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

# EZ Query Methods

EZ query methods provide an alternative way to construct common query types.


## select

```lua
core.mysql.select(db_name, select_tbl)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|select_tbl|The insert table options (see below).|_Table_|__Y__|

__Select Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|tbl|Name of the table to operate on.|_String_|__Y__|
|columns|Array of columns to select as strings.|_Table_|__N__|
|where|Any additional WHERE clause to apply.|_String_|__N__|
|orderby|The sorting attributes. See __Orderby Table__.|_Table_|__N__|
|limit|Limit the amount of the return to this number.|_Number_|__N__|
|distinct|Do not select duplicate column values. Default: false|_Boolean_|__N__|

__Orderby__

The __orderby__ key in the __Select Table__ is a table array filled with __column = direction__ pairs. The direction can be either "ASC" for ascending order or "DESC" for descending order.

__Returns__

A __table__ array of records, or __nil__ and an error.

__Example 1__

```lua
-- Select all records from the 'scores' table
local result, err = core.mysql.select("leaderboard", {
  tbl = 'scores'
})

if not result then
  core.log(err)
else
  -- `result` contains a table array of records
  local record
  for i=1, #result do
    record = result[i]
    core.log(record.player..' scored '..record.score)
  end
end
```

__Example 2__

```lua
-- Select 10 scores greater than 10, in descending order
local result, err = core.mysql.select("leaderboard", {
  tbl = 'scores',
  columns = {'score'},
  where = 'score > 10',
  limit = 10,
  orderby = {
    score = "DESC"
  }
})

if not result then
  core.log(err)
else
  -- `result` contains a table array of records
  local record
  for i=1, #result do
    record = result[i]
    core.log(record.player..' scored '..record.score)
  end
end
```

---

## insert

```lua
core.mysql.insert(db_name, insert_tbl)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|insert_tbl|The insert table options (see below).|_Table_|__Y__|

__Insert Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tbl|Name of the table to operate on.|_String_|__Y__|
|values|A table of __column = value__ pairs.|_Table_|__Y__|

__Returns__

The record id as a __number__, or __nil__ and an error.

__Example__

```lua
local result, err = core.mysql.insert("leaderboard", {
  tbl = 'scores',
  values = {
    score = 200, 
    player = 'Sandy'
  }
})

if not result then
  core.log(err)
else
  -- `result` contains the id for the inserted record
  core.log(result)
end
```

---

## update

```lua
core.mysql.update(db_name, update_tbl)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|update_tbl|The update table options (see below).|_Table_|__Y__|

__Update Table Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tbl|The name of the table to operate on.|_String_|__Y__|
|values|A table of __column = value__ pairs.|_Table_|__Y__|
|where|Where the columns should be updated.|_String_|__N__|

__Returns__

The __number__ of records updated, or __nil__ and an error.

__Example__

```lua
local result, err = core.mysql.update("leaderboard", {
  tbl = 'scores',
  values = {
    score = 230
  },
  where = 'player='..core.mysql.string(player)
})

if not result then
  core.log(err)
else
  -- `result` contains the number of records updated
  core.log(result)
end
```

---

## delete

```lua
core.mysql.delete(db_name, delete_tbl)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|delete_tbl|The delete table options (see below).|_Table_|__Y__|

__Delete Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tbl|The name of the table to operate on.|_String_|__Y__|
|where|A WHERE clause to limit deletions to.|_String_|__N__|
|force|Disables safety check for missing __where__ key.|_Boolean_|__N__|

!!! note
    To run the delete command without a __where__ clause, you must set __force__ to true.

__Returns__

The __number__ of records deleted, or __nil__ and an error.

__Example__

```lua
local result, err = core.mysql.delete("leaderboard", {
  tbl = 'scores',
  where = 'score < 10'
})

if not result then
  core.log(err)
else
  -- `result` contains the number of records deleted
  core.log(result)
end
```

---

# Remote Databases

You can connect to remote MySQL databases by using a connection table in place of the database name in the __core.mysql.query__ and EZ Query methods.

__Connection Table__

|Key|Description|Type|Required|
|----|----------|----|--------|
|database|The remote database name.|_String_|__Y__|
|user|The remote database user.|_String_|__Y__|
|password|The remote database password|_String_|__Y__|
|host|The remote database host address.|_String_|__Y__|
|port|The remote database port. Default: 3306|_Number_|__N__|

__Example__

```lua
local conn_tbl = {
  database = "clients",
  user = "dbuser",
  password = "1234",
  host = "my.sql.host"
}

local results, err = core.mysql.query(conn_tbl, query_str)
```

# Administration

You can and should manage your MySQL databases using a standalone tool. Below are some free resources for managing MySQL databases.


- [SequelPro](https://sequelpro.com/download) (OSX)
- [HeidiSQL](https://www.heidisql.com/download.php) (Windows)

To connect to the MySQL database, use the host address of the server, and the password that was set when installing Coronium Core.
