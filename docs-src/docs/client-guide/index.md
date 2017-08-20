# Listeners

Because network requests are asynchronus actions, listeners must be supplied to all SkyTable API calls. The listener is where you will recieve the response from the SkyTable server.

There are two different event types returned from a SkyTable server. One shaped for the __set__ call, and another for the remaining value based API calls; __get__, __delete__, and __keys__.

While you can use one listener for all the API calls, in general its easier to create two different listeners:

```lua
local profile = skytable:open("profile")

--Set
local function onSetResult(evt)
  if evt.isError then
    print(evt.error)
  else
    print(evt.success)
  end
end

--Get, Delete, Keys
local function onResult(evt)
  if evt.isError then
    print(evt.error)
  else
    local data = evt.data --String, Number, Boolean, Table, or nil
  end
end

--Set something
profile:set("name", "Marco", onSetResult)

--Get something
profile:get("address.street", onResult)
```

The main difference between the events are the properties available.

__Set event properties:__

|Name|Description|Type|
|----|-----------|----|
|isError|An error has occured.|Boolean|
|error|A descriptive error string.|String|
|success|A flag noting a successful __set__ action.|Boolean|
|user|The user key of the calling client.|String|

!!! note
    If set, the __success__ flag will almost always be _true_. An unsuccessful call will usually be propagated to the __error__ property.

__Get, Delete, Keys event properties:__

|Name|Description|Type|
|----|-----------|----|
|isError|An error has occurred.|Boolean|
|error|A descriptive error string.|String|
|data|The returned data.|String, Number, Boolean, or Table|
|user|The user key of the calling client.|String|

!!! note
    The __data__ property can potentially be _nil_.

__Best practices__

While you can set up general listeners, it's best to assign a specific listener per action:

```lua
local profile = skytable:open("profile")

local onSetName(evt)
  if evt.success then
    print('name saved')
  end
end

local onSetColors(evt)
  if evt.success then
    print('colors saved')
  end
end

profile:set("name", "Dave", onSetName)
profile:set("colors", {"red","green","blue"}, onSetColors)
```

This is the recommended approach for the other value based API calls; __get__, __delete__, and __keys__.

Another option is using __[Tags](#tags)__.
