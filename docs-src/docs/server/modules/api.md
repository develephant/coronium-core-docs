The API module allows you to create server-side api methods that can be called using the client-side [API](/client-module/api/) module.

!!! tip "Screencasts Available"
    Get an overview of using the Server API in a screencast format. __[Click here for Part I](/screencasts/#server-api-part-i)__. __[Click here for Part II](/screencasts/#server-api-part-ii)__. 

A server-side api is useful for type checking, validation, and more complex data handling, and in fact, is the only way to access the full functionality of the [MySQL](/server/modules/mysql/) and [Mongo](/server/modules/mongo/) modules.

!!! info "Client-Side API"
    Most data handling can be facilitated directly on the client-side, without the need to create a server-side api. See the client-side __[Data](/client/modules/data/)__ module for Mongo, and the client-side __[MySQL](/client/modules/mysql/)__ module for MySQL.

## Projects

Server-side api methods are housed in "API Projects" that are stored on the server. Projects allow you to create specific functionality for an API , which can then be shared amongst multiple client applications. To switch between projects on the client-side, see the [useProject](/client/modules/core/#useproject) method in the client-side core module.

## Creating Projects

You create projects on the server using the Coronium command line tool, or through the Webmin project editor.

!!! tip "Webmin Project Editor"
    You can set up your projects through your web browser using the Coronium Core Webmin. See the __[API Code Editor](/screencasts/#api-code-editor)__ screencast for more information.

### Add Project

You will first need to log into your server using the __coronium__ user. Once logged in, run the following to create a new project:

```
sudo coronium add <project-name>
```

Replace the <project-name\> above with your desired project name. The name should be alpha-numeric and contain no spaces. This will generate a project template.

The project is stored in the __/home/coronium/projects/<project-name\>__ directory.

At this point, you should connect to the server using SFTP and download the new project directory so that you can create the code for your api.

### Delete Project

To remove a project using the command line tool, run:

```
sudo coronium del <project-name>
```

You can also delete a project by simply removing the project directory from the server.

## Project Files

Project code is stored in the newly created project directory. The required __main.lua__ file will be generated when using the command line tool (see above).

### main.lua

The main.lua file will contain your custom server-side api methods for the project. By default a simple "echo" test method is generated.

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
    print(evt.response.name) -- Jimmy
  end
end
core.api.test({name="Jimmy"}, apiResponse)
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

API methods have a single parameter, which is a __table__ of _input values_ sent up from the client-side [core.api](/client/modules/api/#api) method. Some methods may not need input, in which case the input parameter will be __nil__.

_Example_

```lua
local api = core.api()

function api.addUser(input)
  local name = input.name
  local age = input.age

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

### Errors

At times you may want to indicate to the client that an error has taken place in the server-side api operation. To do this, you return a __core.error__ response object. This will be interpeted by the client as an error event.

```lua
core.error(message, code)
```

The __core.error__ object requires a __string__ message parameter, and an optional __number__ based error code.

_Example_

```lua
local api = core.api()

function api.getUser(input)
  if not input.name then
    return core.error("The user name is missing!")
  end

  return "Hello, " .. input.name
end

return api
```

## Using Modules

Most often you will want to do something more useful than echoing data. You can use the server-side modules to add functionality to your api.

!!! tip "Client-Side API"
    Most data handling can be facilitated directly on the client-side, without the need to create a server-side api. See the client-side __[Data](/client/modules/data/)__ module for Mongo, and the client-side __[MySQL](/client/modules/mysql/)__ module for MySQL.

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

!!! tip ""
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

