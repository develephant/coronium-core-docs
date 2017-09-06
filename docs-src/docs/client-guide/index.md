# Overview

The Coronium Core client plugin provides an interface to your Coronium Core server. The client contains various data modules, a file transfer module, and the ability to call your own custom methods built with the server-side __[API]()__ module.

!!! important
    You will need a running Coronium Core server before being able to utilize the client plugin. You can install the server on __[DigitalOcean]()__ or __[Amazon EC2]()__.

Before continuing, make sure you have read through the __[Client Setup](/client-setup/)__ section.

Coronium Core offers both client-side modules, and custom server-side api methods. In this guide we will be focused on the client-side functionality. On the client-side there are modules for data handling and file transfers, which require no server-side code.

In this example, we will use the __main.lua__ of a Corona project as a reference point, though you may require and use the client plugin in whichever file it is most useful.

# Initialization

How you initialize the client plugin depends on whether you are using custom server-side api methods, or just the built-in client-side modules. You can use both server-side and client-side modules together. Let's look at both initialization options.

__Client modules only__

Client-side modules include support for basic __[Mongo]()__ and __[MySQL]()__ data handling, as well as, the __[File]()__ transfer module. You do not need to write any server-side code.

```lua
local core = require("plugin.coronium-core")

core.init({
  server = "https://your.coronium.host"
})

```

__Server and Client modules__

Using custom server-side api methods allow you to use the full __[Mongo]()__ and __[MySQL]()__ modules, as well as, any other server-side modules available. See the server-side __[API](/server-modules/api/)__ module for more information.

First, make sure you have a [server-side project](/server-modules/api/) set up. 

```lua
local core = require("plugin.coronium-core")

core.init({
  server = "https://your.coronium.host",
  project = "<project-name>",
  key = "project-key-here"
})
```

# Response Events

When calling a module or api method, you must supply an event listener function to recieve the response back from the server:

_Basic api listener_

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error)
  else
    --evt.result contains the server response
  end
end
```

All response events will contain either an __error__ key, or the successful response in the __result__ key. Some modules may have additional event keys. See each modules documentation for event responses.

## error

The __error__ key will always be a string with the error message, or __nil__ if no error is present. 

## result

The __result__ key data depends on the module or api method. See each modules documentation to determine what the __result__ key might hold.

!!! tip
    To debug response events during development, see the __[core.debug](/client-module/core/#debug)__ method.

# API Methods

Depending on the module you are addressing, use the following namespaces on the __core__ object.

```lua
--Client Side Module
core.<module>.<method>([input_params,] listener)

--Server Side API Methods
core.api.<method>([input_params,] listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|input_params|The parameters to pass to the method.|_Table_|__N__|
|listener|The response listener callback function.|_Function_|__Y__|

!!! note
    If a method does not require a __input_params__ table, you can either pass any empty table, or omit the parameter table all together, supplying just the listener.

__Examples__

---

_Client side __[data](/client-module/data/)__ module_

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result) --data object id
  end
end

core.data.save({
  source = "users",
  data = {
    name = "Tammy",
    age = 34,
    active = true
  }
},  apiResponse)
```

---

_Custom server-side __[api](/server-modules/api/)__ method_

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.name) -- Jimmy
  end
end

core.api.echo({name="Jimmy"}, apiResponse)
```

---

!!! tip
    See each client-side module for full usage instructions and examples.