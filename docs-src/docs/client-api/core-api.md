# Overview

Set up the client side plugin.

## init

Initalize the client.

```lua
core.init( config_tbl )
```

__Config Table Keys:__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|server|Your Coronium Core server endpoint.|_String_|__Y__|
|debug|Show message output in the console.|_Boolean_|__N__|

__Example:__

```lua
core.init({
  server = "http://<coronium-host-address>",
  debug = true
})
```
