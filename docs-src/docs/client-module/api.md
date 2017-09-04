# Overview

Provides access to your server-side project api methods. To learn how to create server-side api methods, see the server-side __[API](server-api/api)__ module.

Use the __[core.init](core/#init)__ method to set the server-side project to call the methods against.

## api

```lua
core.api.<method-name>([input_params,] listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|input_params|Parameters to pass to the api method.|_Table_|__N__|
|listener|The response listener callback function.|_Function_|__Y__|

!!! note
    If a method does not require a __input_params__ table, you can either pass any empty table, or omit the parameter table all together, supplying just the listener.

__Event Response__

On success, the __result__ will contain any values that are passed back from the server-side api method.

__Example__

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
