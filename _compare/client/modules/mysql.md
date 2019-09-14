Provides a client-side api to the [EZ query methods](/server/modules/mysql/#ez-query-methods) of the server-side MySQL module. For more complex data handling, you must provide an __[api](/server/modules/api/)__ on the server-side.

!!! warning "Database Required"
    __Before you can use the MySQL module, you will need to create a MySQL database. See the [Databases](/server/webmin/databases/) section for more information on managing your MySQL databases.__

### select

Select multiple records from a database table.

```lua
core.mysql.select(query_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|query_tbl|The query parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Query Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|db|The database to run the query against.|_String_|__Y__|
|tbl|Name of the table to operate on.|_String_|__Y__|
|columns|Array of columns to select as strings.|_Table_|__N__|
|where|Any additional WHERE clause to apply.|_String_ or _Table_|__N__|
|orderby|The sorting attributes. See __Orderby__ below.|_Table_|__N__|
|limit|Limit the records returned. See __Limit__ below.|_Number_ or _Table_|__N__|
|distinct|Filter out duplicate column values. Default: false|_Boolean_|__N__|

__Orderby__

The __orderby__ key should be a table filled with __column = direction__ pairs. The direction can be either "ASC" for ascending order or "DESC" for descending order.

__Limit__

To limit the rows returned, supply a __number__ value to the __limit__ key. To _offset_ the limit, supply a __table__ array of __number__ values. For example, to return rows 6-15: __limit = {5, 10}__.

__Event Response__

On success, the __result__ will contain a __table__ array of records.

__Example__

_Using the "app" database, select all records from the "users" table._

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    for i=1, #evt.result do
      print(evt.result[i].id)
    end
  end
end

local params = {
  db = "app",
  tbl = "users"
}

core.mysql.select(params, apiListener)
```

!!! tldr "More Examples"
    See the server-side __[core.mysql.select](/server/modules/mysql/#select)__ method for more examples.

### selectOne

Select and return a single record from a database table.

```lua
core.mysql.selectOne(query_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|query_tbl|The query parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Query Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|db|The database to run the query against.|_String_|__Y__|
|tbl|Name of the table to operate on.|_String_|__Y__|
|where|The WHERE clause to apply.|_String_ or _Table_|__Y__|
|columns|Array of columns to select as strings.|_Table_|__N__|

__Event Response__

On success, the __result__ will contain a __table__.

!!! info "Special Response"
    Unlike the __[select](#select)__ method, the result is returned as a single record as opposed to an array of records.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.name)
  end
end

local params = {
  db = "app",
  tbl = "users",
  where = { user_id = 20 },
  columns = { "name" }
}

core.mysql.selectOne(params, apiListener)
```

### selectBatch

Query multiple tables of a database and return the individual results in a keyed response.

```lua
core.mysql.selectBatch(batch_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|batch_tbl|The batch parameters for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Batch Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|db|The database to run the query against.|_String_|__Y__|
|batch|A table array of EZ Query select tables, __excluding the db key__. (see [select](#select)). Each select table must also include a __key__ property for the result response (see example below).|_Table_|__Y__|

__Event Response__

On success, the __result__ will be a keyed __table__ with the results of each select query as a table array of records. If `limit` in any of the query entries is set to 1, then a single table with the record data is returned (not an array).

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    local bikes = evt.result.bikes --Array of "Bike" records
    local shoes = evt.result.shoes --Array of "Shoe" records
    local store = evt.result.store --Table "Store" record
  end
end

local batch_select = {
  {
    tbl = "products",
    where = { type = "Bike" },
    key = "bikes"
  },
  {
    tbl = "products",
    where = { type = "Shoes" },
    key = "shoes"
  },
  {
    tbl = "locations",
    where = { name = "Happy Toys" },
    limit = 1, --Return as non-array record
    key = "store"
  }
}

local params = {
  db = "store",
  batch = batch_select
}

core.mysql.selectBatch(params, apiListener)
```

__Query Errors__

If any of the queries in the batch result in an error, the results key for that particular query will contain an __error__ key. Best practice is to check for this key before accessing the results.

```lua
-- Assuming batch call as shown in the example above

local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    if evt.result.bikes.error then
      --an error occurred on this particular query
      print(evt.result.bikes.error)
    else
      --loop over the records
      for i=1, #evt.result.bikes do
        print(evt.result.bikes[i].name)
      end
    end
  end
end
```

### selectMerge

Select from multiple databases and tables and return the results in a keyed table.

```lua
core.mysql.selectMerge(merge_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|merge_tbl|The merge parameters for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Merge Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|merge|A table array of EZ Query select tables. (see [select](#select)). Each select table should also include a `key` property for the result table (see example below).|_Table_|__Y__|

__Event Response__

On success, the __result__ will be a keyed __table__ with the results of each select query. If `limit` in any of the query entries is set to 1, then a single table with the record data is returned (not an array). See __Errors__ below.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    local locs = evt.result.locs

    --check for errors
    if evt.result.errors then
      for i=1, #evt.results.errors do
        local err_tbl = evt.results.error[i]
        print(err_tbl.db, err_tbl.error, err_tbl.index)
      end
    end
  end
end

local merge_dbs = {
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
}

core.mysql.selectMerge({ merge = merge_dbs }, apiListener)
```

__Errors__

If any of the database queries fail, the successful queries will still be returned in their respective key names. On failed queries, the result will also contain an `errors` key, containing a table array describing any errors.

__Example Debug Response__

```text
result:
  errors:
  1:
    db: products
    error: Table 'products.parts' doesn't exist
    index: 2
    status: 1146
  locs:
  1:
    id: 1
    latitude: 80
    longitude: 200
    user_id: d23b8738-4d28-41ed-a967-98e83e855a38
  2:
    id: 3
    latitude: -64
    longitude: 200
    user_id: d23b8738-4d28-41ed-a967-98e83e855a39
  store:
    name = Happy Toys
    city = San Diego
```

### selectCount

Get a record count based on passed in query.

```lua
core.mysql.selectCount(count_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|count_tbl|The count parameters for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Count Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|db|The database to run the count query against.|_String_|__Y__|
|tbl|Name of the table to operate with.|_String_|__Y__|
|where|The WHERE clause to apply.|_String_ or _Table_|__N__|
|column|The column to use as the counting key. Defaults to "id".|_String_|__N__|

If the `where` key is not included, the result is the total records in the provided table.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    local count = evt.result.count
    print("Total red toys:", count)
end

core.mysql.selectCount({
  db = "products",
  tbl = "toys",
  where = { color = "Red" }
}, apiListener)
```

### insert

Insert a single record into a database table.

```lua
core.mysql.insert(query_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|query_tbl|The query parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Query Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db|The database to run the query against.|_String_|__Y__|
|tbl|Name of the table to operate on.|_String_|__Y__|
|values|A table of __column = value__ pairs.|_Table_|__Y__|

!!! warning "Important"
    __Strings in the `values` tables are run through the [mysql.escape](/server/modules/mysql/#escape) method. Do not double-escape values.__

__Event Response__

On success, the __result__ will contain the inserted record id as a __number__.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print("insert id:", evt.result)
  end
end

local params = {
  db = "app",
  tbl = "users",
  values = {
    name = "Bobby",
    age = 34
  },
}

core.mysql.insert(params, apiListener)
```

### insertMany

Insert records into a single table of a database in an optimized way.

```lua
core.mysql.insertMany(insert_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|insert_tbl|The insert parameters table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Insert Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db|The database to run the inserts against.|_String_|__Y__|
|tbl|Name of the table to operate on.|_String_|__Y__|
|records|A table array of `values` tables from the [insert](#insert) method.|_Table_|__Y__|

!!! warning "Important"
    __Strings in the `values` tables are run through the [mysql.escape](/server/modules/mysql/#escape) method. Do not double-escape values.__

__Event Response__

On success, the __result__ will hold an indexed table array of tables containing either an __id__ key; with the id of the newly created record, or an __error__ key; containing the error string.

The response table is indexed the same order as the records table that was sent to the server.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    for i=1, #evt.result do
      local entry = evt.result[i]

      if entry.error then
        print("error in insert entry #"..i..": "..entry.error)
      else
        print("inserted record with id: "..entry.id.." for entry #"..i)
      end
    end
  end
end

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

local params = {
  db = "products",
  tbl = "toys",
  records = toys
}

core.mysql.insertMany(params, apiListener)
```

### insertBatch

Insert records into multiple tables of a database in an optimized way.

```lua
core.mysql.insertBatch(batch_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|batch_tbl|The batch parameters table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Batch Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db|The database to run the batch against.|_String_|__Y__|
|batch|A table array of tables with the `tbl` and `values` keys from the [insert](#insert) method.|_Table_|__Y__|

!!! warning "Important"
    __Strings in the `values` tables are run through the [mysql.escape](/server/modules/mysql/#escape) method. Do not double-escape values.__

__Event Response__

On success, the __result__ will hold an indexed table array of tables containing either an __id__ key; with the id of the newly created record, or an __error__ key; containing the error string.

The response table is indexed the same order as the batch table that was sent to the server.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    for i=1, #evt.result do
      local entry = evt.result[i]

      if entry.error then
        print("error in insert entry #"..i..": "..entry.error)
      else
        print("inserted record with id: "..entry.id.." for entry #"..i)
      end
    end
  end
end

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

local params = {
  db = "products",
  batch = batch_insert
}

core.mysql.insertBatch(params, apiListener)
```

### update

Update record(s) in a database table.

```lua
core.mysql.update(query_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|query_tbl|The query parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Query Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db|The database to run the query against.|_String_|__Y__|
|tbl|The name of the table to operate on.|_String_|__Y__|
|values|A table of __column = value__ pairs.|_Table_|__Y__|
|where|Where the columns should be updated.|_String_ or _Table_|__N__|

!!! warning "Important"
    __Strings in the `values` tables are run through the [mysql.escape](/server/modules/mysql/#escape) method. Do not double-escape values.__

__Event Response__

On success, the __result__ will contain the amount of records updated as a __number__.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print("updated:", evt.result)
  end
end

local params = {
  db = "app",
  tbl = "users",
  values = {
    name = "Paco"
  },
  where = { name = "Bobby" }
}

core.mysql.update(params, apiListener)
```

### updateMany

Update records in a single table of a database in an optimized way.

```lua
core.mysql.updateMany(update_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|update_tbl|The update parameters table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Update Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db|The database to run the updates against.|_String_|__Y__|
|tbl|Name of the table to operate on.|_String_|__Y__|
|update|A table array of tables with the `values` and `where` keys from the [update](#update) method.|_Table_|__Y__|

!!! warning "Important"
    __Strings in the `values` tables are run through the [mysql.escape](/server/modules/mysql/#escape) method. Do not double-escape values.__

__Event Response__

On success, the __result__ will hold an indexed table array of tables containing either an __updated__ key; with the number of records updated, or an __error__ key; containing the error string.

The response table is indexed the same order as the update table that was sent to the server.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    for i=1, #evt.result do
      local entry = evt.result[i]

      if entry.error then
        print("error in update entry #"..i..": "..entry.error)
      else
        print("updated "..entry.updated.." records for entry #"..i)
      end
    end
  end
end

local update_tbl = {
  {
    values = {
      name = "Nike"
    },
    where = { name = "Adidas" }
  },
  {
    values = {
      cost = "1.99"
    },
    where = { id = 3 }
  }
}

local params = {
  db = "products",
  tbl = "shoes",
  update = update_tbl
}

core.mysql.updateMany(params, apiListener)
```

### updateBatch

Update records in multiple tables of a database in an optimized way.

```lua
core.mysql.updateBatch(batch_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|batch_tbl|The batch parameters table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Batch Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db|The database to run the batch against.|_String_|__Y__|
|batch|A table array of tables with the `tbl`, `values` and `where` keys from the [update](#update) method.|_Table_|__Y__|

!!! warning "Important"
    __Strings in the `values` tables are run through the [mysql.escape](/server/modules/mysql/#escape) method. Do not double-escape values.__

__Event Response__

On success, the __result__ will hold an indexed table array of tables containing either an __updated__ key; with the number of records updated, or an __error__ key; containing the error string.

The response table is indexed the same order as the batch table that was sent to the server.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    for i=1, #evt.result do
      local entry = evt.result[i]

      if entry.error then
        print("error in update entry #"..i..": "..entry.error)
      else
        print("updated "..entry.updated.." records for entry #"..i)
      end
    end
  end
end

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

local params = {
  db = "products",
  batch = batch_update
}

core.mysql.updateBatch(params, apiListener)
```

### delete

Delete record(s) from a database table.

```lua
core.mysql.delete(query_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|query_tbl|The query parameters table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Query Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db|The database to run the query against.|_String_|__Y__|
|tbl|The name of the table to operate on.|_String_|__Y__|
|where|A WHERE clause to limit deletions to.|_String_ or _Table_|__N__|
|force|Disables safety check for missing __where__ key.|_Boolean_|__N__|

!!! info "Force Delete"
    __To run the delete command without a __where__ clause, you must set __force__ to true.__

__Event Response__

On success, the __result__ will contain the amount of records deleted as a __number__.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print("deleted:", evt.result)
  end
end

local params = {
  db = "app",
  tbl = "users",
  where = { id = 10 }
}

core.mysql.delete(params, apiListener)
```

!!! tip
    See the server-side __[core.mysql.delete](/server/modules/mysql/#delete)__ method for more examples.

### deleteMany

Delete records from a single table in a database in an optimized way.

```lua
core.mysql.deleteMany(delete_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|delete_tbl|The delete parameters table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Delete Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db|The database to run the deletes against.|_String_|__Y__|
|tbl|Name of the table to operate on.|_String_|__Y__|
|delete|A table array of tables with the `where` key from the [delete](#delete) method.|_Table_|__Y__|

__Event Response__

On success, the __result__ will hold an indexed table array of tables containing either a __deleted__ key; with the number of records deleted, or an __error__ key; containing the error string.

The response table is indexed the same order as the delete table that was sent to the server.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    for i=1, #evt.result do
      local entry = evt.result[i]

      if entry.error then
        print("error in delete entry #"..i..": "..entry.error)
      else
        print("deleted "..entry.deleted.." records for entry #"..i)
      end
    end
  end
end

local delete_tbl = {
  {
    where = { id = 34 }
  },
  {
    where = { color = "Red" }
  }
}

local params = {
  db = "products",
  tbl = "shoes",
  delete = delete_tbl
}

core.mysql.deleteMany(params, apiListener)
```

### deleteBatch

Delete records from multiple tables of a database in an optimized way.

```lua
core.mysql.deleteBatch(batch_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|batch_tbl|The batch parameters table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Batch Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|db|The database to run the batch against.|_String_|__Y__|
|batch|A table array of tables with the `tbl` and `where` keys from the [delete](#delete) method.|_Table_|__Y__|

__Event Response__

On success, the __result__ will hold an indexed table array of tables containing either a __deleted__ key; with the number of records deleted, or an __error__ key; containing the error string.

The response table is indexed the same order as the batch table that was sent to the server.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    for i=1, #evt.result do
      local entry = evt.result[i]

      if entry.error then
        print("error in delete entry #"..i..": "..entry.error)
      else
        print("deleted "..entry.deleted.." records for entry #"..i)
      end
    end
  end
end


local batch_delete = {
  {
    tbl = "toys",
    where = { id = 2 }
  },
  {
    tbl = "shoes",
    where = { type = "running" }
  }
}

local params = {
  db = "products",
  batch = batch_delete
}

core.mysql.deleteBatch(params, apiListener)
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

## Optimized Methods

When working with multiple entries per query, try to choose an optimized query method. The reason for this is due to the fact that each Coronium Client network request to the server requires opening a new database connection per call.

By using the optimized query methods, the Coronium Core server can take advantage of using cached database connections, providing lower overhead and better performance.

Most optimized query methods also make it easier to program your application logic, by grouping results and using less calls.

The following are optimized MySQL module methods:

  - `selectBatch`
  - `selectMerge`
  - `insertMany`
  - `insertBatch`
  - `updateMany`
  - `updateBatch`
  - `deleteMany`
  - `deleteBatch`

## Network Timeout

Though rare, very large workloads may cause the Corona client to throw a network timeout error before you receive a reponse from the server. In these cases you can add a `timeout` parameter to the __query_tbl__ table.

The Corona default network timeout is 30 seconds. _This is an optional parameter_.

### Example

```lua
local function apiListener( evt )
  ...
end

local params = {
  db = "app",
  tbl = "users",
  timeout = 60
}

core.mysql.select(params, apiListener)
```

!!! note "Server-Side Timeout"
    To adjust the MySQL server timeout for large queries, see __[MySQL Timeout](/server/modules/mysql/#mysql-timeout)__.