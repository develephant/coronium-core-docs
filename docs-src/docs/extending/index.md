
# Server Plugins

!!! danger "Read Me"
    What follows is an advanced subject. In almost all cases you should use the [standard project based API](/server/modules/api/). A server plugin is useful if you need to access specific functionality within multiple api projects, or for creating a plugin to share with the community.

Custom built plugins can be used to extend the Coronium Core server. These plugins become available in the __core__ namespace for use in project API files.

Because of this, you must be careful in choosing the name of your plugin. If a name conflict exists, Coronium Core will always choose the internal module. 

If the plugin is to be accessable directly from the Corona client using the Coronium Core plugin, a public-facing API must be created _in addition_ to the internal implementation (see below).

## Internal implementation

The internal implementation goes in the __/usr/local/coronium/lualib/coronium/plugins__ directory.

!!! note
    The implementation plugin code has access to use the __core__ namespace server modules.

__Example__

```lua
--/usr/local/coronium/lualib/coronium/plugins/echo.lua
local echo = {}

function echo.test( input )
  return input
end

return echo
```

## Public-facing API

!!! note
    Creating a public-facing API is optional.

A public-facing API allows you to call your plugin from the Coronium Core client plugin in Corona.

The public-facing API goes in the __/usr/local/coronium/lualib/api__ directory. The API must extend the __core.api__.

__Example__

```lua
--/usr/local/coronium/lualib/api/echo.lua
local api = core.api()

function api.test(input)
  --calling the implementation
  return core.echo.test(input)
end

return api
```

## Enabling plugins

To enable a plugin, add an entry to the __/usr/local/coronium/lualib/coronium.lua__ file.

!!! warning
    Be very careful when editing the __coronium.lua__ file. If the syntax is incorrect the service will not be able to start correctly.

```lua
--/usr/local/coronium/lualib/coronium.lua
...

local plugins = 
{
  --enable the echo plugin implementation
  echo = require("plugins.echo")
}

...
```

Restart the Coronium service with `sudo coronium restart`.

The plugin is now available in the __core__ namespace for use in project API files.

__Server Example__

```lua
local echo = core.echo
```

## Corona Client Usage

!!! note
    You must provide a public-facing API (see above) to access the plugin directly from the Corona client.

To call the public-facing methods of the plugin using the Coronium Core client for Corona, simply use the __core__ namespace of the client.

__Example__

```lua
...

local function onResponse( evt )
  if not evt.error then
    print(evt.result.name) -- Timbo
  end
end

core.echo.test({name="Timbo"}, onResponse)

```
