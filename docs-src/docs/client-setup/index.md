## Get The Plugin

If you don't already have it, get the __Coronium Core Plugin__ from the __[Corona Marketplace](https://marketplace.coronalabs.com/plugin/coronium-core)__.


## Adding The Plugin

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

## Require the Plugin

To gain access to the plugins functionality, require it like so in your Lua file:

```lua
local core = require("plugin.coronium-core")
```

You're now ready to use the __Coronium Core__ plugin.

