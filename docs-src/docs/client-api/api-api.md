# Overview

Used to call server-side api methods.

## api

```lua
core.api.<api-method>( data_tbl, listener )
```

__Parameters:__

|Name|Description|Required|
|----|-----------|--------|
|data_tbl|A data table to pass to the api method.|__Y__|
|listener|The api listener callback function.|__Y__|

!!! note
    If you're api method does not require any data, then pass an empty table ( {} ) to the __data_tbl__ parameter.

__API Listener:__

```lua
local function apiListener( evt )
  if evt.error then
    print( evt.error )
  else
    print(evt.result.<some-key>)
  end
end
```

!!! tip "Server-Side"
    Click __[here]()__ to learn how to set up the matching server-side api method.

__Example:__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.message) -- Hello
  end
end

-- Assuming you have an "echo" server-side method
core.api.echo({message="Hello"}, apiListener)
```