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

The Coronium Core client plugin provides an interface to your Coronium Core server. The client contains various data modules, a file transfer module, user management, and analytics module and the ability to call your own custom methods built with the server-side __[API](/server/modules/api/)__ module.

!!! warning "Coronium Core Required"
    You will need a running __Coronium Core__ server before being able to utilize the client plugin. You can install the server on __[DigitalOcean](/server/installation/digitalocean/)__ or __[Amazon EC2](/server/installation/amazon/)__.

Before continuing, make sure you have read through the __[Installation](#installation)__ section.

In this example, we will use the __main.lua__ of a Corona project as a reference point, though you may require and use the client plugin in whichever file it is most useful.

## Initialization

After you have required the Coronium Core plugin into your Corona project, you must initalize it using the __[core.init](/client/modules/core/)__ method. You will provide the init method the following keys in a table.

__Core Init Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|server|The Coronium Core server address.|_String_|__Y__|
|key|The Coronium Core [server key](/server/guide/key/).|_String_|__Y__|
|scope|A unique application name for your Corona project. See __[Application Scope](#application-scope)__ below.|_String_|__Y__|
|api|The server-side API Project name to run custom api methods against.|_String_|__N__|

### Application Scope

!!! note "Screencast Available"
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

### Enabling Scopes

You must run at least one __[User](/client/modules/users/api/)__ or __[Analytics](/client/modules/analytics/)__ method for the scope to be available in the Webmin. If the scope is not visible from the "Application Scope" dropdown menu, use the __Update Scopes Cache__ option in the Webmin __Config__ section.

### Custom API Init

To utilize custom server-side api methods, first make sure you have a [server-side project](/server/modules/api/) set up. Provide the API Project name to the `api` parameter in the initialization. See the server-side __[API](/server/modules/api/)__ module for more information.

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

When calling a module or api method, you must supply a listener function to recieve the response event back from the server:

_Basic api listener_

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error) --contains error string, if any.
  else
    local res = evt.result --evt.result contains the server response
  end

  print(evt.tt) --contains request round trip time
end
```

All response events will contain either an __error__ key, or the successful response in the __result__ key.

All response events contain a __tt__ key. See below for more details on this key.

!!! tldr "Event Keys"
    Some modules may have additional event keys. See each modules documentation for event responses.

### error

The __error__ key will always be a string with the error message, or __nil__ if no error is present. If there is an error, often there will also be a __status__ key with a numeric code.

### result

The __result__ key data depends on the module or api method. See each modules documentation to determine what the __result__ key might hold.

### tt

The __tt__ key allows you to see the "trip time" for the request. This is the total round trip time in milliseconds from the start of the client network request, to the final client response. 

!!! tip "Debug Responses"
    To debug response events during development, use the __[core.debug](/client/modules/core/#debug)__ method (see below).

## Debug Responses

Sometimes it can be nice to see the full response (including errors) without have to write the full logic in the event listener.

To make this simpler, you can use the `core.debug` method.

__Example__

```lua
local function apiResponse( evt )
  core.debug( evt )
end

core.users.login({
  username = 'Wordy',
  password = '1234'
}, apiResponse)
```

The following will be output to the __Corona__ console:

```text
result:
  active: true
  email: sleeptankxyz@gmail.com
  group: foodies
  scope: Space Race
  user_id: c369637a-cb27-44ee-aeaf-ca263daa49d5
  username: Wordy
  validated: true
  tt: 64.401
```

## API Methods

Depending on the module you are addressing, use the following namespaces on the __core__ object.

```lua
--Standard Client Side Module Methods
core.<module>.<method>([input_params,] listener)

--Custom Server Side API Methods
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

_Client side __[data](/client/modules/data/)__ module_

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

_Custom server-side __[api](/server/modules/api/)__ method_

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

See each client-side module documentation for full usage instructions and examples.
