The __mysql__ module allows you to run queries against the local MySQL server instance. You can also connect to remote databases.

!!! info "Database Needed"
    Before you can use the MySQL module, you will need to create a MySQL database. See the [Administration](#administration) section for more information on connecting to your database.

### query | q

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

!!! info ""
    To connect to remote databases see __[Remote Databases](#remote-databases)__.

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

!!! info ""
    Any other commands will be returned as __table__. It is recommended that you manage your databases using an external tool. See __[Administration](#administration)__ below.

!!! tip
    Use __[core.sf](utils/#sf-string-format)__ (string format) to make queries easier to build, and type safe.

__Select Example__

```lua
local query = core.sf("SELECT * FROM orders LIMIT %d", 100)

local result, err = core.mysql.query("clients", query)

if not result then
  core.log(err)
end

local record
for i=1, #result do
  record = result[i] --a record as a table
  core.log(record.id)
end

```

__Insert Example__

```lua
local columns = "name, age, email"
local values = {"Jimmy", 22, "me@somemail.com"}

--escape string values
values = core.mysql.escapeAll(values)

local query = core.sf("INSERT INTO users (%s) VALUES (%s, %d, %s);", 
  columns, 
  unpack(values)
)

local result, err = core.mysql.query("clients", query)

if not result then
  core.log(err)
end

core.log("inserted id is: ", result)
```

### escape

Escape a string value to be sql safe. Returns an escaped __string__.

```lua
core.mysql.escape(unescaped_str)
```

__Parameters__

|Name|Description|Requried|
|----|-----------|--------|
|unescaped_str|The __string__ value to escape.|__Y__|

__Example__

```lua
local str = core.mysql.escape("Eat at Joe's")
```

!!! info "Important"
    The returned value is enclosed in single quotes. Do not wrap the value with additional quotes or it may cause problems with your query.

### escapeAll

Escape all string values in a table array to be sql safe. Returns __table__ array.

```lua
core.mysql.escapeAll(tbl_values)
```

__Parameters__

|Name|Description|Requried|
|----|-----------|--------|
|tbl_values|A __table__ array of mixed value types. String values will be escaped.|__Y__|

__Example__

```lua
local values = {
  'A special "thing" here.',
  24,
  "Joe's Place"
}

values = core.mysql.escapeAll( values )
```

### timestamp

MySQL compatible UTC based timestamp.

```lua
core.mysql.timestamp()
```

### date

MySQL compatible UTC based date.

```lua
core.mysql.date()
```

### localTimestamp

MySQL compatible timestamp based on the local server time.

```lua
core.mysql.localTimestamp
```

### localDate

MySQL compatible date based on the local server date.

```lua
core.mysql.localDate
```

## EZ Query Methods

EZ query methods provide an alternative way to construct common query types. For more complex queries, use the __core.mysql.query__ method above.

!!! tip "Client-Side API"
    You can access the EZ query methods directly using the client-side __[MySQL](/client-module/mysql)__ module.


### select

Select multiple records from a database table.

```lua
core.mysql.select(db_name, select_tbl)
```

__Returns__

A __table__ array of records, or __nil__ and an error.

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|select_tbl|The select table options (see below).|_Table_|__Y__|

__Select Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|tbl|Name of the table to operate on.|_String_|__Y__|
|columns|Array of columns to select as strings.|_Table_|__N__|
|where|Any additional WHERE clause to apply.|_String_|__N__|
|orderby|The sorting attributes. See __Orderby__ below.|_Table_|__N__|
|limit|Limit the records returned. See __Limit__ below.|_Number_ or _Table_|__N__|
|distinct|Filter out duplicate column values. Default: false|_Boolean_|__N__|

__Orderby__

The __orderby__ key should be a table filled with __column = direction__ pairs. The direction can be either "ASC" for ascending order or "DESC" for descending order. See example 2 below.

__Limit__

To limit the rows returned, supply a __number__ value to the __limit__ key. To _offset_ the limit, supply a __table__ array of __number__ values. For example, to return rows 6-15: __limit = {5, 10}__.

__Example 1__

```lua
-- Select all records from the 'scores' table
local result, err = core.mysql.select("leaderboard", {
  tbl = "scores"
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
  tbl = "scores",
  columns = { "score" },
  where = "score > 10",
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

### selectOne

Select and return a single record from a database table.

```lua
core.mysql.selectOne(db_name, select_tbl)
```

__Returns__

A single record as a __table__, or __nil__ and an error.

!!! note ""
    Unlike the __[select](#select)__ method, the result is returned as a single record as opposed to an array of records.

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|select_tbl|The select table options (see below).|_Table_|__Y__|

__Select Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|tbl|Name of the table to operate on.|_String_|__Y__|
|where|The WHERE clause to apply.|_String_|__Y__|
|columns|Array of columns to select as strings.|_Table_|__N__|

__Example__

```lua
local record, err = core.mysql.selectOne("app", {
  tbl = "users",
  where = "user_id="..user_id,
  columns = { "name" }
})
```

### insert

Insert a single record into a database table.

```lua
core.mysql.insert(db_name, insert_tbl)
```

__Returns__

The record id as a __number__, or __nil__ and an error.

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

!!! note ""
    Strings in the __values__ table will be automatically run through __core.mysql.escape__.

__Example__

```lua
local result, err = core.mysql.insert("leaderboard", {
  tbl = "scores",
  values = {
    score = 200, 
    player = "Sandy"
  }
})

if not result then
  core.log(err)
else
  -- `result` contains the id for the inserted record
  core.log(result)
end
```

### insertMany

Insert multiple records in a database table in an optimized way.

```lua
core.mysql.insertMany(db_name, insert_tbl)
```

__Returns__

The amount of records inserted as a __number__, or __nil__ and an error.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|insert_tbl|The insert table options (see below).|_Table_|__Y__|

__Insert Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tbl|Name of the table to operate on.|_String_|__Y__|
|records|A table array of `values` tables. See the [insert](#insert) method above.|_Table_|__Y__|

!!! note ""
    Strings in the `values` tables will be automatically run through __core.mysql.escape__.

__Example__

```lua
local toys = {
  {
    name = "Car",
    color = "red"
  },
  {
    name = "Teddy Bear",
    color = "brown"
  }
}


local result, err = core.mysql.insertMany("products", {
  tbl = "toys",
  records = toys
})

if not result then
  core.log(err)
else
  -- `result` contains the total records inserted
  core.log(result)
end
```

### update

Update record(s) in a database table.

```lua
core.mysql.update(db_name, update_tbl)
```

__Returns__

The __number__ of records updated, or __nil__ and an error.

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

!!! note ""
    Strings in the __values__ table will be automatically run through __core.mysql.escape__.

__Example__

```lua
local result, err = core.mysql.update("leaderboard", {
  tbl = "scores",
  values = {
    score = 230
  },
  where = "id=20"
})

if not result then
  core.log(err)
else
  -- `result` contains the number of records updated
  core.log(result)
end
```

### delete

Delete record(s) from a database table.

```lua
core.mysql.delete(db_name, delete_tbl)
```

__Returns__

The __number__ of records deleted, or __nil__ and an error.

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

!!! info "Important"
    To run the delete command without a __where__ clause, you must set __force__ to true.

__Example__

_Delete using a __where__ clause_

```lua
local result, err = core.mysql.delete("leaderboard", {
  tbl = "scores",
  where = "score < 10"
})

if not result then
  core.log(err)
else
  -- `result` contains the number of records deleted
  core.log(result)
end
```

_Delete __all__ records from the table_

```lua
local result, err = core.mysql.delete("leaderboard", {
  tbl = "scores",
  force = true
})

if not result then
  core.log(err)
else
  -- `result` contains the number of records deleted
  core.log(result)
end
```

## MySQL Timeout

For large queries you may need to adjust the timeout for the call. You can do this by passing a connection table in place of the database name and include the `timeout` parameter in milliseconds. The default is 2000 (2 seconds).

### Example

```lua
local query = core.sf("SELECT * FROM orders LIMIT %d", 100)

local conn_tbl = {
  database = "clients",
  timeout = 10000 --10 secs
}

local result, err = core.mysql.query(conn_tbl, query)
```

!!! info "EZ Query Methods"
    You can do the same for the __[EZ Query](#ez-query-methods)__ methods. Replace the database name with a connection table as shown above.

## Remote Databases

You can connect to remote MySQL databases by using a connection table in place of the database name in the __core.mysql.query__ and EZ Query methods.

__Connection Table__

|Key|Description|Type|Required|
|----|----------|----|--------|
|database|The remote database name.|_String_|__Y__|
|user|The remote database user.|_String_|__Y__|
|password|The remote database password|_String_|__Y__|
|host|The remote database host address.|_String_|__Y__|
|port|The remote database port. Default: 3306|_Number_|__N__|
|timeout|The timeout in milliseconds for the query. Default: 2000|_Number_|__N__|

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

## Administration

You can and should manage your MySQL databases using a standalone tool. Below are some free resources for managing MySQL databases.

!!! tip "Screencast Available"
    Learn more about database administration in a screencast format by __[Clicking here](/screencasts/#database-administation)__.

- [SequelPro](https://sequelpro.com/download) (OSX)
- [HeidiSQL](https://www.heidisql.com/download.php) (Windows)

To connect to the MySQL database, use the host address of the server, and the password that was set when installing Coronium Core.
