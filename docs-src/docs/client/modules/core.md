Provides methods to configure the client-side plugin.

### init

Initialize the client.

```lua
core.init( config_tbl )
```

__Config Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|server|The Coronium Core server address.|_String_|__Y__|
|key|The Coronium Core [server key](/server/guide/key/).|_String_|__Y__|
|scope|A unique application name for your Corona project. See __[Application Scope](/client/guide/#application-scope)__ in the client guide.|_String_|__Y__|
|api|The server-side API Project name to run api methods against.|_String_|__N__|

__Example__

```lua
core.init({
  server = "https://<coronium-host-address>",
  key = "<coronium-server-key>",
  scope = "Space Race",
  api = "default"
})
```

!!! tip ""
    For detailed initialization instructions, see the __[Initialization](/client/guide/#initialization)__ section of the __Client Guide__.

### useProject

Change the server-side project to run api methods against.

```lua
core.useProject( project )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|project|The project name to run api methods against.|_String_|__Y__|

__Example__

```lua
core.useProject("default")
```

### debug

Pretty print the response event to the console. Useful for debugging.

```lua
core.debug(response_event)
```

__Example__

```lua
local function apiResponse( evt )
  core.debug( evt )

  ...
end
```
