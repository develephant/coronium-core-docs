This guide outlines the client-side plugin setup and general usage.

## Installation

If you don't already have it, get the __Coronium Core Plugin__ from the __[Corona Marketplace](https://marketplace.coronalabs.com/plugin/coronium-core)__.


<h4>Add the plugin</h4>

Add the plugin by adding an entry to the __plugins__ table of __build.settings__ file:

```
settings =
{
    plugins =
    {
        ["plugin.coronium-core"] =
        {
            publisherId = "com.develephant"
        },
    },
}
```

<h4>Require the plugin</h4>

To gain access to the plugins functionality, require it like so in your Lua file:

```lua
local core = require("plugin.coronium-core")
```

You're now ready to use the __Coronium Core__ plugin.

## Plugin Overview

The Coronium Core client plugin provides an interface to your Coronium Core server. The client contains various data modules, a file transfer module, user management, and analytics module and the ability to call your own custom methods built with the server-side __[API](/server-modules/api/)__ module.

!!! info "Important"
    You will need a running Coronium Core server before being able to utilize the client plugin. You can install the server on __[DigitalOcean](/server-install/digitalocean/)__ or __[Amazon EC2](/server-install/ec2/)__.

Before continuing, make sure you have read through the __[Installation](#installation)__ section.

Coronium Core offers both client-side modules, and custom server-side api methods. In this guide we will be focused on the client-side functionality.

In this example, we will use the __main.lua__ of a Corona project as a reference point, though you may require and use the client plugin in whichever file it is most useful.

## Initialization

How you initialize the client plugin depends on whether you are using custom server-side api methods, or just the built-in client-side modules. You can use both server-side and client-side modules together. 

### Application Scope

Every Corona project must provide a unique "application scope" to the Coronium Core initialization. This scope allows you to group users and metrics to each application so that you can visualize them individually via the Webmin, and for other purposes.

It is important to choose a short but descriptive application scope name. ___Once the scope is set, you can not change it for that particular application___.

You will provide this application scope name to the `scope` parameter of the initialzation.

With that in mind, let's look at both initialization options.

__Client modules only__

Client-side modules include support for basic __[Mongo](/client-module/data/)__ and __[MySQL](/client-module/mysql/)__ data handling, the __[Users](/client-module/users/)__ module for user management, an __[Analytics](/client-module/analytics/)__ module for app metrics, as well as, the __[File](/client-module/files/)__ transfer module. You do not need to write any server-side code to use these modules.

```lua
local core = require("plugin.coronium-core")

core.init({
  server = "https://your.coronium.host",
  key = "<coronium-server-key>",
  scope = "<application-scope>"
})

```

__Server and Client modules__

Using custom server-side api methods allow you to use the full __[Mongo](/server-modules/mongo/)__ and __[MySQL](/server-modules/mysql/)__ modules, as well as, any other server-side modules available to create your own custom APIs. See the server-side __[API](/server-modules/api/)__ module for more information.

First, make sure you have a [server-side project](/server-modules/api/) set up. Provide the API Project name to the `api` parameter in the initialization.

```lua
local core = require("plugin.coronium-core")

core.init({
  server = "https://your.coronium.host",
  key = "<coronium-server-key>",
  scope = "<application-scope>",
  api = "<api-project-name>",
})
```

## Response Events

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

### error

The __error__ key will always be a string with the error message, or __nil__ if no error is present. If there is an error, often there will also be a __status__ key with a numeric code.

### result

The __result__ key data depends on the module or api method. See each modules documentation to determine what the __result__ key might hold.

!!! tip
    To debug response events during development, see the __[core.debug](/client-module/core/#debug)__ method.

## API Methods

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

!!! tldr "Special Note"
    If a method does not require a __input_params__ table, you can either pass any empty table, or omit the parameter table all together, supplying just the listener.

__Examples__

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

!!! tip ""
    See each client-side module documentation for full usage instructions and examples.
