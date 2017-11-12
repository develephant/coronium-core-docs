Provides methods for simple application analytics.

### appOpened

Logs an "opened" event and auto-populates application meta data.

```lua
core.analytics.appOpened([listener])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|listener|An optional listener callback function.|_Function_|__N__|

__Collected Meta Data__

The __appOpened__ method automatically collects the following meta data and stores it in the __analytics__ database.

|Name|Description|Example|
|----|-----------|-------|
|name|The application name as reported by Corona|FunRun|
|country_code|The originating country code, if any.|US|
|model|The device model the application is running on.|iPhone|
|platform|The platform the application is running on.|ios|
|platform_ver|The platform version the application is running on.|10.12.6|
|version|The application version string as reported by Corona.|com.app.funrun|
|manufacturer|The device manufacturer the application is running on.|Apple|
|screen_h|The screen pixel height as reported by Corona.|480|
|screen_w|The screen pixel width as reported by Corona.|320|
|corona_build|The Corona build the application is running on.|2017.3126|
|added|The timestamp of when the application was opened in UTC format.|2017-01-01 12:00:00|

### addEvent

Register a custom analytics event.

```lua
core.analytics.addEvent(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|A descriptive name for the event.|_String_|__Y__|
|scope|An optional event scope.|_String_|__N__|
|tags|An array of up to five key/value table objects.|_Table_|__N__|

__Basic Example__

```lua
core.analytics.addEvent({
  name = "gameOver"
}, onAddEvent)
```

__Advanced Example__

```lua
core.analytics.addEvent({
  name = "gameOver",
  scope = "game.funrun",
  tags = {
    {score = 1000},
    {won = true}
  }
}, onAddEvent)
```