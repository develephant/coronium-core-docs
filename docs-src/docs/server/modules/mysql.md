The __mysql__ module allows you to run queries against the local MySQL server instance. You can also connect to remote databases.

!!! warning "Database Required"
    __Before you can use the MySQL module, you will need to create a MySQL database. See the [Administration](#administration) section for more information on connecting to your database.__

!!! info "Client-Side MySQL"
    Most data handling can be handled directly on the client-side, without the need to create a server-side api. See the client-side __[MySQL](/client/modules/mysql/)__ module.

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

!!! tip "Remote Databases"
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

!!! info "Other Commands"
    Any other commands will be returned as __table__. It is recommended that you manage your databases using an external tool. See __[Administration](#administration)__ below.

!!! tip
    Use __[core.sf](/server/modules/utils/#sf-string-format)__ (string format) to make queries easier to build, and type safe.

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

Escape a string value to be sql safe.

```lua
core.mysql.escape(unescaped_str)
```

__Parameters__

|Name|Description|Requried|
|----|-----------|--------|
|unescaped_str|The __string__ value to escape.|__Y__|

__Returns__

A sql safe escaped __string__.

__Example__

```lua
local str = core.mysql.escape("Eat at Joe's")
```

!!! warning "Escaping Values"
    The returned value is enclosed in single quotes. Do not wrap the value with additional quotes or it may cause problems with your query. __Many EZ Query methods automatically use mysql.escape on values, be sure to check the documentation for each method.__

### escapeAll

Escape all string values in a table array to be sql safe.

```lua
core.mysql.escapeAll(tbl_values)
```

__Parameters__

|Name|Description|Requried|
|----|-----------|--------|
|tbl_values|A __table__ array of mixed value types. String values will be escaped.|__Y__|

__Returns__

Returns a __table__ array of the escaped string values.

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
core.mysql.timestamp([seconds])
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|seconds|UNIX timestamp.|_Number_|__N__|

__Returns__

UTC timestamp based on provided UNIX time. Otherwise, returns current UTC timestamp.

### date

MySQL compatible UTC based date.

```lua
core.mysql.date([seconds])
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|seconds|UNIX timestamp.|_Number_|__N__|

__Returns__

UTC date based on provided UNIX time. Otherwise, returns current UTC date.

### localTimestamp

MySQL compatible timestamp based on the local server time.

```lua
core.mysql.localTimestamp([seconds])
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|seconds|UNIX timestamp.|_Number_|__N__|

__Returns__

Local timestamp based on provided UNIX time. Otherwise, returns current local timestamp.

### localDate

MySQL compatible date based on the local server date.

```lua
core.mysql.localDate([seconds])
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|seconds|UNIX timestamp.|_Number_|__N__|

__Returns__

Local date based on provided UNIX time. Otherwise, returns current local date.

### logQueries

A special method that will enable the logging of the query strings output by MySQL methods (or other methods that use a database) until it is toggled off.

```lua
core.mysql.logQueries(state)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|state|Set the query logging on or off. Default is `true`.|_Boolean_|__N__|

!!! warning
    This should only be used for debugging purposes or you'll end up with a lot of log entries.

__Example__

```lua
core.mysql.logQueries() --start logging queries

local res, err, code = core.mysql.select("products", {
  tbl = "toys",
  where { id = 20 }
})

-- The following output will be added to the Coronium log file
-- SELECT * FROM `toys` WHERE `id`=20;

core.mysql.logQueries(false) --stop logging queries
--Any MySQL methods run after will not be written to the log.
```


## EZ Query Methods

EZ query methods provide an alternative way to construct common query types. For more complex queries, use the __core.mysql.query__ method above.

!!! tip "Client-Side API"
    You can access the EZ query methods directly using the client-side __[MySQL](/client/modules/mysql/)__ module.


### select

Select multiple records from a database table.

```lua
core.mysql.select(db_name, select_tbl)
```

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
|where|Any additional WHERE clause to apply.|_String_ or _Table_|__N__|
|orderby|The sorting attributes. See __Orderby__ below.|_Table_|__N__|
|limit|Limit the records returned. See __Limit__ below.|_Number_ or _Table_|__N__|
|distinct|Filter out duplicate column values. Default: false|_Boolean_|__N__|

__Orderby__

The __orderby__ key should be a table filled with __column = direction__ pairs. The direction can be either "ASC" for ascending order or "DESC" for descending order. See example 2 below.

__Limit__

To limit the rows returned, supply a __number__ value to the __limit__ key. To _offset_ the limit, supply a __table__ array of __number__ values. For example, to return rows 6-15: __limit = {5, 10}__.

__Returns__

A __table__ array of records, or __nil__ and an error.

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
  where = "`score` > 10",
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

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|select_tbl|The select table options (see below).|_Table_|__Y__|

__Select Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|tbl|Name of the table to operate on.|_String_|__Y__|
|where|The WHERE clause to apply.|_String_ or _Table_|__Y__|
|columns|Array of columns to select as strings.|_Table_|__N__|

__Returns__

A single record as a __table__, or __nil__ and an error.

!!! info "Special Response"
    Unlike the __[select](#select)__ method, the result is returned as a single record as opposed to an array of records.

__Example__

```lua
local record, err = core.mysql.selectOne("app", {
  tbl = "users",
  where = { user_id = user_id },
  columns = { "name" }
})
```

### selectBatch

Query multiple tables of a database and return the individual results in a keyed response.

```lua
core.mysql.selectBatch(db_name, batch_tbl)
```

!!! tip
    This method offers optimization when used on the client-side. See the __[selectBatch](/client/modules/mysql/#selectbatch)__ client-side method.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|	The database to run the query against.|_String_|__Y__|
|batch_tbl|A table array of EZ Query select tables, __excluding the db key__. (see [select](#select)). Each select table must also include a __key__ property for the result table (see example below).|_Table_|__Y__|

!!! info "Special Response"
    If a `limit` key is set to 1, the results will be a single table record, and not a table array. See [selectOne](/server/modules/mysql/#selectone).

__Returns__

A keyed __table__ with the results of each select query as a table array of records. If `limit` in any of the query entries is set to 1, then a single table with the record data is returned (not an array).

__Example__

```lua
local batch_select = {
  {
    tbl = "products",
    where = { type = 'Bike' },
    key = "bikes"
  },
  {
    tbl = "products",
    where = { type = 'Shoes' },
    key = "shoes"
  },
  {
    tbl = "locations",
    where = { name = "Happy Toys" },
    limit = 1, --Return as non-array record
    key = "store"
  }
}

local result, err = core.mysql.selectBatch("store", batch_select)

if not result then
  core.log(err)
else
  core.log(result.bikes) --Array of "Bike" records
  core.log(result.shoes) --Array of "Shoe" records
  core.log(result.store) --Table "Store" record
end
```

__Query Errors__

If any of the queries in the batch result in an error, the results key for that particular query will contain an __error__ key. Best practice is to check for this key before accessing the results.

```lua
-- Assuming batch call as shown in the example above

if not result then
  core.log(err)
else
  if result.bikes.error then
    --an error occurred on this particular query
    core.log(result.bikes.error)
  else
    --loop over the records
    for i=1, #result.bikes do
      core.log(result.bikes[i].name)
    end
  end
end
```

### selectMerge

Select from multiple databases and tables and return the results in a keyed table.

```lua
core.mysql.selectMerge(merge_tbl)
```

!!! tip
    This method offers optimization when used on the client-side. See the __[selectMerge](/client/modules/mysql/#selectmerge)__ client-side method.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|merge_tbl|A table array of EZ Query select tables, with an additonal `db` key. (see [select](#select)). Each select table should also include a `key` property for the result table (see example below).|_Table_|__Y__|

__Returns__

A key/value based table containing the `key` names assigned in the query tables. Each key section will contain a table array of returned records. If `limit` in any of the query entries is set to 1, then a single table with the record data is returned (not an array). See also __Errors__ below.

__Example__

```lua
local res, err, code = core.mysql.selectMerge({
  {
    db = "locations",
    tbl = "spots",
    key = "locs"
  },
  {
    db = "products",
    tbl = "parts",
    where = { part_id = 20 },
    key = "part"
  },
  {
    db = "stores",
    tbl = "toy",
    limit = 1, --Return as non-array record
    where = { name = "Happy Toys" },
    key = "store"
  }
})
```

__Errors__

If any of the database queries fail, the successful queries will still be returned in their respective key names. On failed queries, the result will also contain an `errors` key, containing a table array describing any errors.

__Example Response__

```text
{
  errors = {
    {
      db = products
      error = Table 'products.parts' doesn't exist
      index = 2
      status = 1146
    }
  },
  locs = {
    {
      id = 1
      latitude = 80
      longitude = 200
      user_id = "d23b8738-4d28-41ed-a967-98e83e855a38"
    },
    {
      id = 3
      latitude = -64
      longitude = 200
      user_id = "d23b8738-4d28-41ed-a967-98e83e855a39"
    }
  },
  store = {
    name = "Happy Toys",
    city = "San Diego"
  }
}
```

### selectCount

Get a record count based on passed in query.

```lua
core.mysql.selectCount(db_name, count_tbl)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|db_name|The database to run the count query against.|_String_|__Y__|
|count_tbl|The count table options (see below).|_Table_|__Y__|

__Count Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|tbl|Name of the table to operate on.|_String_|__Y__|
|where|The WHERE clause to apply.|_String_ or _Table_|__N__|
|column|The column to use as the counting key. Defaults to "id".|_String_|__N__|

If the `where` key is not included, the result is the total records in the provided table.

__Returns__

The amount of records counted as a __number__, or __nil__, error, and error code.

__Example__

```lua
local count, err = core.mysql.selectCount("products", {
  tbl = "toys",
  where = { color = "Red" }
})

core.log("Total red toys "..count)
```

### insert

Insert a single record into a database table.

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

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Returns__

The record id as a __number__, or __nil__ and an error.

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

Insert records into a single table of a database.

```lua
core.mysql.insertMany(db_name, insert_tbl)
```

!!! tip
    This method offers optimization when used on the client-side. See the __[insertMany](/client/modules/mysql/#insertmany)__ client-side method.

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

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Returns__

An indexed table array of tables containing either an __id__ key; with the id of the newly created record, or an __error__ key; containing the error string, or __nil__ and an error.

The result table is indexed the same order as the records table that was supplied to the call.

__Example__

```lua
local records_arr = {
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
  records = records_arr
})

if not result then
  core.log(err)
else
  for i=1, #result do
    local entry = result[i]

    if entry.error then
      core.log("error in insert entry #"..i..": "..entry.error)
    else
      core.log("inserted record with id: "..entry.id.." for entry #"..i)
    end
  end
end
```

### insertBatch

Insert records into multiple tables of a database.

```lua
core.mysql.insertBatch(db_name, batch_tbl)
```

!!! tip
    This method offers optimization when used on the client-side. See the __[insertBatch](/client/modules/mysql/#insertbatch)__ client-side method.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|	The database to run the query against.|_String_|__Y__|
|batch_tbl|A table array of tables with the `tbl` and `values` keys from the [insert](#insert) method.|_Table_|__Y__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Returns__

An indexed table array of tables containing either an __id__ key; with the id of the newly created record, or an __error__ key; containing the error string, or __nil__ and an error.

The result table is indexed the same order as the batch table that was supplied to the call.

__Example__

```lua
local batch_insert = {
  {
    tbl = "shoes"
    values = {
      name = "Adidas",
      size = 12
    }
  },
  {
    tbl = "toys",
    values = {
      name = "Car",
      age_group = 10
    }
  },
  {
    tbl = "toys",
    values = {
      name = "Bear",
      age_group = 5,
      color = "Brown"
    }
  }
}

local result, err = core.mysql.insertBatch("products", batch_insert)

if not result then
  core.log(err)
else
  for i=1, #result do
    local entry = result[i]

    if entry.error then
      core.log("error in insert entry #"..i..": "..entry.error)
    else
      core.log("inserted record with id: "..entry.id.." for entry #"..i)
    end
  end
end
```

### update

Update record(s) in a database table.

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
|where|Where the columns should be updated.|_String_ or _Table_|__N__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Returns__

The __number__ of records updated, or __nil__ and an error.

__Example__

```lua
local result, err = core.mysql.update("leaderboard", {
  tbl = "scores",
  values = {
    score = 230
  },
  where = { id = 20 }
})

if not result then
  core.log(err)
else
  -- `result` contains the number of records updated
  core.log(result)
end
```

### updateMany

Update records in a single table of a database.

```lua
core.mysql.updateMany(db_name, update_tbl)
```

!!! tip
    This method offers optimization when used on the client-side. See the __[updateMany](/client/modules/mysql/#updatemany)__ client-side method.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|update_tbl|The update table options (see below).|_Table_|__Y__|

__Update Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tbl|Name of the table to operate on.|_String_|__Y__|
|update|A table array of tables with the `values` and `where` keys from the [update](#update) method.|_Table_|__Y__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Returns__

An indexed table array of tables containing either an __updated__ key; with the number of records updated, or an __error__ key; containing the error string, or __nil__ and an error.

The result table is indexed the same order as the update table that was supplied to the call.

__Example__

```lua
local update_arr = {
  {
    values = {
      name = "Nike"
    },
    where = { name="Adidas" }
  },
  {
    values = {
      cost = "1.99"
    },
    where = { id = 3 }
  }
}


local result, err = core.mysql.updateMany("products", {
  tbl = "shoes",
  update = update_arr
})

if not result then
  core.log(err)
else
  for i=1, #result do
    local entry = result[i]

    if entry.error then
      core.log("error in update entry #"..i..": "..entry.error)
    else
      core.log("updated "..entry.updated.." records for entry #"..i)
    end
  end
end
```

### updateBatch

Update records in multiple tables of a database.

```lua
core.mysql.updateBatch(db_name, batch_tbl)
```

!!! tip
    This method offers optimization when used on the client-side. See the __[updateBatch](/client/modules/mysql/#updatebatch)__ client-side method.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|	The database to run the query against.|_String_|__Y__|
|batch_tbl|A table array of tables with the `tbl`, `values` and `where` keys from the [update](#update) method|_Table_|__Y__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Returns__

An indexed table array of tables containing either an __updated__ key; with the number of records updated, or an __error__ key; containing the error string, or __nil__ and an error.

The result table is indexed the same order as the batch table that was supplied to the call.

__Example__

```lua
local batch_update = {
  {
    tbl = "shoes",
    values = {
      name = "Adidas"
    },
    where = { id = 3 }
  },
  {
    tbl = "toys",
    values = {
      name = "Raggedy Ann",
      gender = "female"
    },
    where = { name = "Raggedy Andy" }
  },
  {
    tbl = "toys",
    values = {
      company = "Tonka"
    },
    where = { id = 4 }
  }
}

local result, err = core.mysql.updateBatch("products", batch_update)

if not result then
  core.log(err)
else
  for i=1, #result do
    local entry = result[i]

    if entry.error then
      core.log("error in update entry #"..i..": "..entry.error)
    else
      core.log("updated "..entry.updated.." records for entry #"..i)
    end
  end
end
```

### delete

Delete record(s) from a database table.

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
|where|A WHERE clause to limit deletions to.|_String_ or _Table_|__N__|
|force|Disables safety check for missing __where__ key.|_Boolean_|__N__|

!!! info "Force Delete"
    To run the delete command without a __where__ clause, you must set __force__ to true.

__Returns__

The __number__ of records deleted, or __nil__ and an error.

__Example__

_Delete using a __where__ clause_

```lua
local result, err = core.mysql.delete("leaderboard", {
  tbl = "scores",
  where = "`score` < 10"
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

### deleteMany

Delete records from a single table of a database.

```lua
core.mysql.deleteMany(db_name, delete_tbl)
```

!!! tip
    This method offers optimization when used on the client-side. See the __[deleteMany](/client/modules/mysql/#deletemany)__ client-side method.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to run the query against.|_String_|__Y__|
|delete_tbl|The delete table options (see below).|_Table_|__Y__|

__Delete Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tbl|Name of the table to operate on.|_String_|__Y__|
|delete|A table array of tables with the `where` key from the [delete](#delete) method.|_Table_|__Y__|

__Returns__

An indexed table array of tables containing either a __deleted__ key; with the number of records deleted, or an __error__ key; containing the error string, or __nil__ and an error.

The result table is indexed the same order as the update table that was supplied to the call.

__Example__

```lua
local delete_arr = {
  {
    where = { id = 34 }
  },
  {
    where = { color= "Red" }
  }
}


local result, err = core.mysql.deleteMany("products", {
  tbl = "shoes",
  delete = delete_arr
})

if not result then
  core.log(err)
else
  for i=1, #result do
    local entry = result[i]

    if entry.error then
      core.log("error in delete entry #"..i..": "..entry.error)
    else
      core.log("deleted "..entry.deleted.." records for entry #"..i)
    end
  end
end
```

### deleteBatch

Delete records from multiple tables of a database.

```lua
core.mysql.deleteBatch(db_name, batch_tbl)
```

!!! tip
    This method offers optimization when used on the client-side. See the __[deleteBatch](/client/modules/mysql/#deletebatch)__ client-side method.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|	The database to run the query against.|_String_|__Y__|
|batch_tbl|A table array of tables with the `tbl` and `where` keys from the [delete](#delete) method.|_Table_|__Y__|

__Returns__

An indexed table array of tables containing either a __deleted__ key; with the number of records deleted, or an __error__ key; containing the error string, or __nil__ and an error.

The result table is indexed the same order as the batch table that was supplied to the call.

__Example__

```lua
local batch_delete = {
  {
    tbl = "toys",
    where = { id = 2 }
  },
  {
    tbl = "shoes",
    where = { kind = "running" }
  }
}

local result, err = core.mysql.deleteBatch("products", batch_delete)

if not result then
  core.log(err)
else
  for i=1, #result do
    local entry = result[i]

    if entry.error then
      core.log("error in delete entry #"..i..": "..entry.error)
    else
      core.log("deleted "..entry.deleted.." records for entry #"..i)
    end
  end
end
```

## The WHERE Key

Many of the MySQL (and other) modules use a `where` key to specify the "WHERE" clause for a database query. Depending on what data type and structure you provide this key, a couple different things can happen automagically.

### String Based

When passing a string to the `where` key, you are on your own to create a valid MySQL query string. The query string is interpreted as-is. 

You never include the literal "WHERE" in the `where` key value.

__Examples__

```text
where = "`color`='Red'"
```

```text
where = "`kind`='Truck' AND `color`='Red'"
```

```text
where = "`kind`='Truck' OR `kind`='Car'"
```

### Table Based

The advantage of using a table based `where` key is that all of the values are properly formatted to make a valid and type-safe MySQL query.

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

To reproduce the three string based examples above as table based:

```lua
where = { color = "Red" }
```

```lua
where = { kind = "Truck", color = "Red" } --AND
```

```lua
where = { kind = { "Truck", "Car" } } --OR
```

__Ordered Table Queries__

With "ordered" query tables you can replicate some more complex queries:

```text
where = "`color='Red' AND `model`='Ford' AND kind='Truck' OR kind='Car'"
```

To reproduce the query above, put the entries in a table array (order matters):

```lua
where = {
  { color = "Red", model = "Ford" },
  { kind = { "Truck", "Car" } }
}
```

For an all OR query like:

```text
where = "`color='Red' OR `model`='Ford' OR kind='Truck' OR kind='Car'"
```

You can use an ordered table with a single entry:

```lua
where = {
  { color = { "Red" }, model = { "Ford" }, kind = { "Truck", "Car" } }
}
```

At this time multiple entries in an "ordered" `where` table are combined using 'AND', which may cause problems with more complicated queries, so you'll need to fall back to the string method.

Table type queries also do not support conditionals, so the following cannot be replicated with a table based query:

```text
where = "`score` > 100"
```


## Advanced Methods

While the MySQL module methods above are fairly performant, they do automatically manage the database connections, and create the raw query strings, which creates a slight hit on speed.

By managing the database connection directly you can get the best performance from your queries, especially if you are performing multiple queries in your API methods.

!!! warning "Closing Connections"
    Always use __dbClose__ at the end of your session, or you'll leave a connection open, using up resources.

### dbConnect

Create a new connection to a database.

```lua
core.mysql.dbConnect( db_name )
```

__Returns__

A new database connection object, or __nil__, and an error.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_name|The database to connect to. You can also use a connection table. See the _Connection Table_ section in [Remote Databases](#remote-databases).|_String_|__Y__|

__Example__

```lua
local db_conn, err = core.mysql.dbConnect("products")
```

### dbQuery

Perform a query using the database connection (see __[dbConnect](#dbconnect)__). You can, and should, run multiple query calls using the same database connection.

```lua
core.mysql.dbQuery( db_connection, query_str )
```

__Returns__

The query result, or __nil__, and an error. See the _Result Values_ section of the __[query](#query-q)__ method.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_connection|The database connection returned from __[dbConnect](#dbconnect)__.|_Object_|__Y__|
|query_str|The MySQL query to send to the database.|_String_|__Y__|

__Example__

```lua
local result, err = core.mysql.dbQuery(db_conn, "SELECT * FROM products;")
```

### dbClose

Close a previous database connection opened with __[dbConnect](#dbconnect)__.

```lua
core.mysql.dbClose( db_connection )
```

__Returns__

A truthy value on success, or __nil__, and an error.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db_connection|The database connection returned from __[dbConnect](#dbconnect)__.|_Object_|__Y__|

__Example__

```lua
local ok, err = core.mysql.dbClose(db_conn)
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

!!! note "Screencast Available"
    Learn more about database administration in a screencast format by __[Clicking here](/screencasts/#database-administation)__.

- [SequelPro](https://sequelpro.com/download) (OSX)
- [HeidiSQL](https://www.heidisql.com/download.php) (Windows)

To connect to the MySQL database, use the host address of the server, and the password that was set when installing Coronium Core.

### Password Update

To change your MySQL password log in your Coronium Core server as and run:

```sh
sudo mysql-updatepw
```