# Overview

Set up the client side plugin.

## init

Initalize the client.

```lua
core.init( config_tbl )
```

__Config Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|server|Your Coronium Core server endpoint.|_String_|__Y__|
|debug|Show message output in the console.|_Boolean_|__N__|

__Example__

```lua
core.init({
  server = "http://<coronium-host-address>",
  debug = true
})
```

---

## useProject

Select the server-side project to issue commands against.

```lua
core.useProject( name, key )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|The project name on the server instance.|_String_|__Y__|
|key|The key as set in the project config.|_String_|__Y__|

__Example__

```lua
core.setProject("default", "1234")
```
