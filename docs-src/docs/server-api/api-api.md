Used to build your application api.

```lua
local api = core.api()

function api.echo( input )
  return input
end

return api
```