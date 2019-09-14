The API module allows you to create server-side api methods that can be called using the client-side [API](/client-module/api/) module.

A server-side api is useful for more complex application logic, and in fact, is the only way to access the full functionality of the [MySQL](/server/modules/mysql/) and [Mongo](/server/modules/mongo/) modules. Some modules are only available server-side.

## Projects

Server-side api methods are housed in "API Projects" that are stored on the server. Projects allow you to create specific functionality for an API, which can then be shared amongst multiple client applications. 

## Managing Projects

You manage projects by using the Coronium __[Webmin](/server/webmin/setup/)__ __API Builder__.

### Add Project

Log into your browser based __[Webmin](/server/webmin/setup/)__ and go to the __API Builder__ section. Click the __New Project__ button and follow the instructions.

After creating your project, you can use the __API Builder__ code editor to build your custom API code.

### Delete Project

Log into your browser based __[Webmin](/server/webmin/setup/)__ and go to the __API Builder__ section. Click the __Delete__ button for the project you want to delete, and follow the instructions.

## Project Files

### main.lua

!!! tip "Webmin Code Editor"
    You can edit the `main.lua` and other project files using a visual code editor. Go to the __API Builder__ section in the __[Webmin](/server/webmin/setup/)__ and click the project file you would like to edit.

The __main.lua__ file will contain your custom server-side api methods for the project. By default a simple "echo" test method is generated.

_main.lua example_

```lua
-- Coronium Core API
local api = core.api()

function api.test( input )
  return input
end

return api
```

You can call this method from the client using the client-side __[API](/client/modules/api/)__ module.

_Client-side example_

```lua
core.init({
  server = "https://your.coronium.host",
  key = "<coronium-server-key>",
  scope = "<application-scope>",
  api = "default"
})

local apiResponse(evt)
  if evt.error then
    print(evt.error)
  else
    print(evt.result.name) -- Jimmy
  end
end
core.api.test({name="Jimmy"}, apiResponse)
```

### Adding Files

There may be times when you would like to split up your API project code into seperate files. You can add new files to a project using the __[Webmin](/server/webmin/setup/)__. 

Go to the __API Builder__ section of the Webmin and click the __Add File__ button for the project you would like to add a file to and requiring it into the projects __main.lua__ file.

The important thing to know about external file modules is that they cannot return output to the client directly. ___Only the main.lua file can send output back down to the client___.

Because of this it is important that you set up your external file modules properly to return results back to the __main.lua__ file for sending results downstream.

_Example File Module_

```lua
--project/default/db.lua
local db = {}

function db.getSomeData(params)

  local res, err = core.mysql.selectOne("products", {
    tbl = "toys",
    where = { color = params.color }
  })

  --we must return the result back to the main.lua
  return res, err

end

return 
```

_Example Main Lua File_

```lua
--project/default/main.lua
local db = require("project.db")

local api = core.api()

function api.doSomething(input)

  local res, err = db.getSomeData({color = "Red"})

  --here we can return data to the downstream client
  if not res then
    return core.error(err)
  end

  return res
end

return api
```

## core.api

To create your api, you need to extend the __core.api__ object. As shown in the main.lua file above, this is done like so:

```lua
local api = core.api()
```

Add your api methods to the __api__ object, and finally return the __api__ object at the end of the file.

_Example_

```lua
local api = core.api()

function api.addUser(input)
  ... --code here
end

return api
```

### Input

Custom API methods are passed two parameters, which are a table of __input__ values sent up from the client-side [core.api](/client/modules/api/#api) method, and the __scope__ of the current client call (see [Application Scope](/client/guide/#application-scope)). 

Some methods may not send input, in which case the input parameter will be __nil__. Most often you will only use the __input__ parameter.

!!! note
    The __input__ parameter is supplied from the client-side call (see [client-side API](/client/modules/api/)). The __scope__ is determined internally. You _do not_ send the scope value in the client-side API call.

_Example_

```lua
local api = core.api()

-- Input only
function api.addUser(input)
  local name = input.name
  local age = input.age

  ...
end

-- Input and Scope
function api.getUser(input, scope)
  local res, err = core.users.getWithQuery(scope, {
    username = input.name
  })

  ...
end

return api
```

### Return

___All api methods must return a response.___ This can either be the result of the api operation, or an error (see errors below). You can only return a _single_ response value.

A response value can be one of the following Lua types: _String_, _Number_, _Boolean_, or _Table_. String and Table values must be JSON encodable. The value returned will be sent back down to the client for consumption.

_Example_

```lua
local api = core.api()

function api.doSomething(input)

  return "Welcome to Coronium"
  --OR
  return 42
  --OR
  return true
  --OR
  return {name = "Sally", age = 34}

end

return api
```

To learn how to consume the response on the client, see the client-side __[API](/client/modules/api/)__ module.

## core.error

Return an error response object to the client.

```lua
core.error(message, code)
```

### Errors

At times you may want to indicate to the client that an error has taken place in the server-side api operation. To do this, you return a __core.error__ response object. This will be interpeted by the client as an error event.

The __core.error__ object requires a __string__ message parameter, and an optional __number__ based error code.

_Example_

```lua
local api = core.api()

function api.echoName(input)
  if not input.name then
    -- Return error with string only:
    return core.error("The name parameter is missing!")

    -- OR

    -- Return error with a custom status code too:
    return core.error("The name parameter is missing!", 110)
  end

  return "Hello, " .. input.name
end

return api
```

Some of the built in modules return an error string _and_ an error code which you can pass downstream to the client. See each modules documentation for more information.

_Example_

```lua
local api = core.api()

function api.getUser(input)

  local user, err, code = core.users.get("aad3eba3-9c9c-9a1b-f236de1e3752")

  if not user then
    return core.error(err, code)
  end

  return user
end

return api
```

When using custom error codes, make sure to check the latest reserved __[Status Codes](/client/codes/)__ and choose something not listed there. Custom status codes should generally be in the 100-199 range.

## Using Modules

Most often you will want to do something more useful than echoing data. You can use the server-side modules to add functionality to your api.

### Examples

__List the available collections in a Mongo database:__

_Server-side_

```lua
--main.lua
local api = core.api()

function api.getCollections(input)

  --get the database name from the input
  local db_name = input.db_name

  --get a mongo db instance
  local db, err = core.mongo(db_name)

  --check for errors
  if not db then
    return core.error(err)
  end

  --get the collection list
  local list, err = db:listCollections()

  --check for errors
  if not list then
    return core.error(err)
  end

  --otherwise return the list
  return list

end

return api
```

Some things to note in the code above:

- Errors from the server-side modules can be passed to the client with __core.error__.
- Any return values from server-side modules can be used in the __return__.

To issue a call to this api method on the client, we use the client-side __[API](/client/modules/api/)__ module:

_Client-side_

```lua
local function apiResponse(evt)
  if evt.error then
    print(evt.error)
  else
    --got collection list
    for i=1, #evt.result do
      print(evt.result[i]) -- collection name
    end
  end
end

core.api.getCollections({db_name="app"}, apiResponse)
```

__Get a user using a MySQL query:__

_Server-side_

```lua
--main.lua
local api = core.api()

function api.queryUser(input)

  --get id number from the input
  local id = input.id

  --check for id value
  if not id then
    return core.error("Id is missing!")
  end

  --build sql query
  local q = core.sf("SELECT * FROM users WHERE id=%d;", input.id)

  --run the query
  local record, err = core.mysql.query("app", q)

  --check for error
  if not record then
    return core.error(err)
  end

  --return the record
  return record
end

return api
```

_Client-side_

```lua
local function apiResponse(evt)
  if evt.error then
    print(evt.error)
  else
    --result is a record
    print(evt.result.name) --output 'name' column
  end
end

core.api.queryUser({id=20}, apiResponse)
```

!!! tip "Client-Side MySQL"
    The following example can also be done directly from the client-side __[MySQL](/client/modules/mysql/#insert)__ module.

Assuming we have a MySQL database properly created called "app", we can quickly insert a new record to the "users" table like so:

_Server-side_

```lua
--main.lua
local api = core.api()

function api.addUser(input)

  --insert data using EZ query method
  local id, err = core.mysql.insert("app", {
    tbl = "users",
    --pass the input directly, escaping string values
    values = core.mysql.escapeAll(input)
  })

  --check for error
  if not id then
    return core.error(err)
  end

  --return new record id
  return id

end

return api
```

_Client-side_

```lua
local function apiResponse(evt)
  if evt.error then
    print(evt.error)
  else
    print(evt.result) --result is id
  end
end

local data = {
  name = "Johnson",
  age = 32,
  active = true
}

core.api.addUser(data, apiResponse)
```

