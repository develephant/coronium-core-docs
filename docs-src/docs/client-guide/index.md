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

In this example, we will use the __main.lua__ of a Corona project as a reference point, though you may require and use the client plugin in whichever file it is most useful.

## Initialization

After you have required the Coronium Core plugin into your Corona project, you must initalize it using the __[core.init](/client-module/core/)__ method. You will provide the init method the following keys in a table.

__Core Init Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|server|The Coronium Core server address.|_String_|__Y__|
|key|The Coronium Core [server key](/server-guide/#server-key).|_String_|__Y__|
|scope|A unique application name for your Corona project. See __[Application Scope](#application-scope)__ below.|_String_|__Y__|
|api|The server-side API Project name to run custom api methods against.|_String_|__N__|

### Application Scope

!!! tip "Screencast Available"
    Learn about application scopes in a screencast format by __[Clicking here](/screencasts/#application-scopes)__.

Every Corona project must provide a unique "application scope" to the Coronium Core initialization. This scope allows you to group users and metrics to each application so that you can visualize them individually via the Webmin, and for other purposes.

_It is important to choose a short but descriptive application scope name that is unique from your other application scopes._ 

!!! danger "Don't Change The Scope!"
    __Once the scope is set, do not change it for that particular application. If you do, you will lose user and metric associations.__

You will provide this application scope name to the `scope` parameter of the initialzation.

__Example__

```lua
local core = require("plugin.coronium-core")

core.init({
  server = "https://your.coronium.host",
  key = "03624656-ca90-11e7-b8d4-fb59abeb4c03",
  scope = "Fun Run"
})

```

### Custom API Init

To utilize custom server-side api methods, first make sure you have a [server-side project](/server-modules/api/) set up. Provide the API Project name to the `api` parameter in the initialization. See the server-side __[API](/server-modules/api/)__ module for more information.

__Example__

```lua
local core = require("plugin.coronium-core")

core.init({
  server = "https://your.coronium.host",
  key = "03624656-ca90-11e7-b8d4-fb59abeb4c03",
  scope = "Fun Run",
  api = "funrun",
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
