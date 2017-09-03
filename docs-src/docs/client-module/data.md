# Overview

Provides a client-side api to the server-side __[Data](/server-modules/data)__ module. The __data__ module is a simple interface to the Mongo database. For more complex data handling, you must provide an __[api](/server-modules/api/)__ on the server-side.

## get

```lua
core.data.get(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|source|The data source to run against.|_String_|__Y__|
|query|A query table or string id.|_Table_ or _String_|__Y__|
|db|A specific database. Default: "_app".|_String_|__N__|

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result._id) -- result holds the data object
  end
end

core.data.get({source="users", query="id1234"}, apiListener)
```

---

## save

```lua
core.data.save(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|source|The data source to run against.|_String_|__Y__|
|data|A data object to save.|_Table_|__Y__|
|db|A specific database. Default: "_app".|_String_|__N__|

__Example__

_Saving a new data object_

!!! note
    Do not add an ___id__ key to the object, it will be generated automatically on the server-side.

```lua
local dataObj = {
  name = "Sally",
  age = 32,
  active = true,
  colors = {"blue", "gold"}
}

local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print("saved")
  end
end

core.data.save({source="users", data=dataObj}, apiListener)
```

_Updating an existing object_

!!! note
    To update an existing object, first retrieve it using __core.data.get__.

```lua
local obj = --an object from core.data.get

obj.score = 200 --add or update key value

local function apiListener( evt )
  --listener code (see above)
end

core.data.save({source="users", data=obj}, apiListener)
```

!!! warning
    Do not change the ___id__ key in the object you wish to update.

---

## delete

```lua
core.data.delete(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|source|The data source to run against.|_String_|__Y__|
|id|The data object id string.|_String_|__Y__|
|db|A specific database. Default: "_app".|_String_|__N__|

!!! note
    You can get an object id from any data object. See the __core.data.get__ example above.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print("deleted")
  end
end

core.data.delete({source="users", id="id1234"}, apiListener)
```

!!! tip
    While passing an id string is the most consistent way to delete an object, you can also pass the entire object to the __id__ key. Example: __{source = "users", id = obj}__. The object must contain a valid __\_id__ field for this to work.

---

# Pagination

## getPage

```lua
core.data.getPage(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Default|Type|Required|
|----|-----------|----|-------|--------|
|source|The data source to run against.|none|_String_|__Y__|
|page|The page number to return.|none|_Number_|__Y__|
|perPage|The number of objects per page.|20|_Number_|__N__|
|sort|Sort constant or sorting table.|core.ASC|_Const_ or _Table_|__N__|
|query|Specialized query table.|{ } (all records)|_Table_|__N__|
|db|Specify a different database.|"_app"|_String_|__N__|

__Sort Constants__

|Enum|Description|
|----|-----------|
|core.ASC|Sort in an ascending order.|
|core.DESC|Sort in a descending order.|

__Example__

```lua
local params = {
  source = "users",
  page = 1,
  perPage = 10
}

local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    --evt.result holds array of objects
    for i=1, #evt.result do
      print(evt.result[i]._id)
    end
  end
end

core.data.getPage(params, apiListener)
```