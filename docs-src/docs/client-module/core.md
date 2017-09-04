# Overview

Provides methods to configure the client-side plugin.

## init

Initialize the client.

```lua
core.init( config_tbl )
```

__Config Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|server|Your Coronium Core server endpoint.|_String_|__Y__|
|project|The project name to run api methods against.|_String_|__N__|
|key|The project key as set in the server-side config.lua.|_String_|__N__|
|debug|Show message output in the console.|_Boolean_|__N__|

__Example__

```lua
core.init({
  server = "http://<coronium-host-address>",
  project = "default",
  key = "1234",
  debug = true
})
```

!!! tip
    For detailed initialization instructions, see the __[Initialization](/client-guide/#initialization)__ section of the __Client Guide__.

---

## useProject

Change the server-side project to run api methods against.

```lua
core.useProject( project, key )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|project|The project name to run api methods against.|_String_|__Y__|
|key|The project key as set in the server-side config.lua.|_String_|__Y__|

__Example__

```lua
core.useProject("default", "1234")
```
