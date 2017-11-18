Provides access to your server-side project api methods. To learn how to create server-side api methods, see the server-side __[API](/server/modules/api/)__ module.

!!! tip "Screencast Available"
    Learn how to use the Client API in a screencast format by __[Clicking here](/screencasts/#client-plugin-api)__.

Use the __[core.init](/client/modules/core/#init)__ method to set the server-side project to call the methods against.

### api

```lua
core.api.<method-name>([input_params,] listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|input_params|Parameters to pass to the api method.|_Table_|__N__|
|listener|The response listener callback function.|_Function_|__Y__|

!!! tldr "Special Note"
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

-- Assuming you have an "test" server-side method
core.api.test({message="Hello"}, apiListener)
```

## Network Timeout

Though rare, very large workloads may cause the Corona client to throw a network timeout error before you receive a reponse from the server. In these cases you can add a `timeout` parameter to the __input_params__ table.

The Corona default network timeout is 30 seconds. _This is an optional parameter_.

### Example

```lua
local function apiListener( evt )
  ...
end

core.api.test({message="Hello", timeout=60}, apiListener)
```