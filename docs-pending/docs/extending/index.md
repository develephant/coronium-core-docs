
# Server Plugins

!!! danger "Read Me"
    What follows is an advanced subject. In almost all cases you should use the [standard project based api](server-api/overview/). A server plugin is useful if you need to access specific functionality within multiple api projects, or for creating a plugin to share with the community.

Custom built plugins can be added to the server, which makes them available in the __core__ namespace for use in project api files.

If the plugin is to be accessable directly from the Corona client, a public-facing api must be created in addition to the internal implementation.

## Internal implementation

The internal implementation goes in the __/usr/local/coronium/lualib/coronium/plugins__ directory.

!!! note
    The implementation code has access to the __core__ namespace server modules.

__Example__

```lua
--/usr/local/coronium/lualib/coronium/plugins/echo.lua
local echo = {}

function echo.test( input )
  return input
end

return echo
```

## Public facing api

!!! note
    Creating a public facing api is optional.

The public facing api goes in the __/usr/local/coronium/lualib/api__ directory. The api must extend the __core.api__.

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
  echo = require("coronium.plugins.echo")
}

...
```

The plugin is now available in the __core__ namespace for use in project api files.

__Example__

```lua
local echo = core.echo
```

# Corona client usage

!!! note
    You must provide a public facing api (see above) to access the plugin directly from the Corona client.

To call the public facing methods of the plugin using the Coronium Core client for Corona, use the __core__ namespace of the client.

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
