
# Server Plugins

!!! danger "Read Me"
    What follows is an advanced subject. In almost all cases you should use the [standard project based API](/server/modules/api/). A server plugin is useful if you need to access specific functionality within multiple api projects, or for creating a plugin that is internal to your company, or to share with the community.

Custom built plugins can be used to extend the __Coronium Core__ server. These plugins become available in the server-side `core` namespace for use in server-side project API files.

Because of this, you must be careful in choosing the name of your plugin. If a name conflict exists, __Coronium__ will always choose the internal module. 

## Creating Plugins

!!! note
    Custom plugins have access to the __core__ namespace [server modules](/server/modules/api/).

A custom plugin is simply a Lua module you place in a certain directory structure on your __Coronium Core__ server. 

Your custom plugins live in the __/home/coronium/plugins__ directory.

__Example Plugin__

```lua
--Basic plugin
local echo = {}

function echo.test( input )
  return input
end

return echo
```

### Namespaces

Your plugin should be placed in a unique directory to avoid conflicts with other developer plugins.

__Example__

```
plugins/
  example/
    echo.lua
```

In the example above the __echo__ plugin is placed inside the __plugins/example__ directory.

You can use a developer nickname, company, etc. as the directory name. You can store multiple plugins you create in this folder for use; providing each plugin has a unique name.

__Example__

```
plugins/
  develephant/
    echo.lua
    otherplugin.lua
```

## Enabling plugins

To enable a plugin, add an entry to the __/home/coronium/plugins/plugins.lua__ file. Choose the __key__ the plugin will use, and then literally `require` it.

!!! warning
    Be very careful when editing the __plugins.lua__ file. If the syntax is incorrect the service will not be able to start correctly.

__Example__

```lua
--/home/coronium/plugins/plugins.lua
local plugins = 
{
  --enable the echo plugin implementation
  echo = require("develephant.echo")
}

return plugins
```

Restart the __Coronium__ service with `sudo coronium restart`.

### Accessing

The plugin is now available in the `core` server-side namespace as the key specified in the __plugins.lua__ file for use in server-side project API files.

__Server API Example__

```lua
local api = core.api()

function api.test( input )
  -- Using the custom `echo` plugin
  local resp = core.echo.test( input.username )

  return resp
end

return api
```
