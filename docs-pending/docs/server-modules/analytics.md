Provides methods for simple application analytics.

### addEvent

```lua
core.analytics.addEvent(name, scope, tags)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|A descriptive name for the event.|_String_|__Y__|
|scope|An optional event scope.|_String_|__N__|
|tags|An array of up to five key/value table objects.|_Table_|__N__|

__Basic Example__

```lua
core.analytics.addEvent( "gameOver" )
```

__Advanced Example__

```lua
local tags = {
  {score = 1000},
  {won = true}
}

core.analytics.addEvent("gameOver", "game.funrun", tags)
```