Provides a client-side api to the [EZ query methods](/server/modules/mysql/#ez-query-methods) of the server-side MySQL module. For more complex data handling, you must provide an __[api](/server/modules/api/)__ on the server-side.

!!! info "Database Needed"
    Before you can use the MySQL module, you will need to create a MySQL database. See the [Administration](/server/modules/mysql/#administration) section for more information on connecting to your database.

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
|where|Any additional WHERE clause to apply.|_String_|__N__|
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

!!! tip
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
|where|The WHERE clause to apply.|_String_|__Y__|
|columns|Array of columns to select as strings.|_Table_|__N__|

__Event Response__

On success, the __result__ will contain a __table__.

!!! note ""
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
  where = "user_id=20",
  columns = { "name" }
}

core.mysql.selectOne(params, apiListener)
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

!!! note ""
    Strings in the __values__ table will be automatically run through the server-side __[core.mysql.escape](/server/modules/mysql/#escape)__ method.

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

Insert multiple records in a database table in an optimized way.

```lua
core.mysql.insertMany(query_tbl, listener)
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
|records|A table array of `values` tables. See the [insert](#insert) method above.|_Table_|__Y__|

!!! note ""
    Strings in the `values` tables will be automatically run through the server-side __[core.mysql.escape](/server/modules/mysql/#escape)__ method.

__Event Response__

On success, the __result__ will contain the amount of records inserted as a __number__.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print("inserted:", evt.result)
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
|where|Where the columns should be updated.|_String_|__N__|

!!! note ""
    Strings in the __values__ table will be automatically run through the server-side __[core.mysql.escape](/server/modules/mysql/#escape)__ method.

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
  where = "name='Bobby'"
}

core.mysql.update(params, apiListener)
```

### delete

Delete record(s) from a database table.

```lua
core.mysql.delete(query_tbl, listener)
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
|where|A WHERE clause to limit deletions to.|_String_|__N__|
|force|Disables safety check for missing __where__ key.|_Boolean_|__N__|

!!! info "Important"
    To run the delete command without a __where__ clause, you must set __force__ to true.

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
  where = "id=10"
}

core.mysql.delete(params, apiListener)
```

!!! tip
    See the server-side __[core.mysql.delete](/server/modules/mysql/#delete)__ method for more examples.

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