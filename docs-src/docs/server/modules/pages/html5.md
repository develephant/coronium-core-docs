You can host your __[Corona HTML5](https://forums.coronalabs.com/forum/637-html5/)__ builds directly on your __Coronium Core__ server in the _Pages_ directory.

This also allows you to access your custom built [server-side APIs](/server/modules/api/) using the __[CoroniumJS](/server/modules/pages/html5/#using-coroniumjs)__ plugin.

<i class="fas fa-play-circle"></i> [Click here to watch a screencast.](/screencasts/#html5-builds-coroniumjs)

## Hosting HTML5 Builds

To host your HTML5 game or app, create a directory in the __/home/coronium/pages__ directory and upload your Corona HTML5 output.

The game or app will be available at: 

```
http[s]://your.coronium.server/<html5-app-directory>
```

To access the _debug build_ use:

```
http[s]://your.coronium.server/<html5-app-directory>/index-debug.html
```

## Using CoroniumJS

Using the __CoroniumJS__ plugin in your Corona HTML5 project gives you access to your custom [server-side APIs](/server/modules/api/). The plugin will be enhanced over time to allow for more functionality.

### Download

You can download the most recent __CoroniumJS__ plugin archive by __[clicking here](https://s3.amazonaws.com/coronium-core/plugins/coroniumjs.zip)__.

### Install 

Expand the archive, and then move the __coroniumjs_js.js__ and __coroniumjs.lua__ files the ___root___ of your Corona HTML5 project directory.

Your directory tree should look something like:

```
<corona_project>/
  coroniumjs_js.js
  coroniumjs.lua
  main.lua
  ...
```

### Require

In your __main.lua__ (or wherever needed):

```lua
local corejs = require("coroniumjs")
```

## CoroniumJS API

### init

Initialize the CoroniumJS plugin.

```lua
corejs.init( config_tbl )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|config_tbl|The configuration table for the plugin initialzation.|_Table_|__Y__|

__Config Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|key|The Coronium Core __[server key](/server/guide/key/)__.|_String_|__Y__|
|scope|A unique application name for your Corona project. See __[Application Scope](/client/guide/#application-scope)__ in the client guide.|_String_|__Y__|
|api|The server-side API Project name to run api methods against (see __[Server-side API](/server/modules/api/)__).|_String_|__Y__|

__Example__

```lua
corejs.init({
  key = '<coronium-server-key>',
  scope = 'FunRun',
  api = 'game'
})
```

__Note__

The __CoroniumJS__ plugin will only work with HTML5 projects hosted on a __Coronium Core__ server.

---

### api

Call a custom server-side API method.

```lua
corejs.api(method_name, params_tbl, event_id)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|method_name|The method to call on the custom server-side API.|_String_|__Y__|
|params_tbl|A key/value table of parameters for the method.|_Table_|__Y__|
|event_id|An event identifier for the event listener (see below).|_String_|__Y__|

__Event Listener__

```lua
corejs.addEventListener(event_id, listener_func)
```

An event listener is required when calling the `api` method. The event listener parameters are the `event_id` identifier, and the actual event listener function itself.

__Example__

_Assuming a `setData` method on a custom [server-side API](/server/modules/api/):_

```lua
corejs.addEventListener('onSetData', function(event)
  if event.error then
    print(event.error)
  else
    print(event.result.<key>)
  end
end)

corejs.api('setData', {name="Jenny"}, 'onSetData')
```