## Get The Plugin

If you don't already have it, get the __Coronium SkyTable Plugin__ from the __[Corona Marketplace](https://marketplace.coronalabs.com/plugin/coronium-skytable)__.


## Adding The Plugin

Add the plugin by adding an entry to the __plugins__ table of __build.settings__ file:

```
settings =
{
    plugins =
    {
        ["plugin.skytable"] =
        {
            publisherId = "com.develephant"
        },
    },
}
```

You're now ready to use the __Coronium SkyTable__ plugin.

# Getting Started

A SkyTable is a _user scoped_ data table. This means the data is tied to a ___single___ user. It is less of a database, and more of a secure per user table datastore.

!!! important
    Make sure you understand how __Coronium SkyTable__ works before commiting to using it in your project. ___You cannot access any other data than the user provided credentials allow.___

While a SkyTable is tied to a specific user, you can create as many specific, per user, SkyTables as you need for a given application (See __[open](api/#open)__).

__Example of basic usage:__

```lua
--Require plugin
local skytable = require("plugin.skytable")

--Initialize SkyTable
skytable:init({
  user = "<user-email>",
  password = "<user-password>",
  base = "app1",
  key = "<server-key>",
  host = "http://<server-host>:7173",
  debug = true
})

--Open a "profile" SkyTable
local profile = skytable:open("profile")

--Get action listener
local function onResult(evt)
  if evt.isError then
    print(evt.error)
  else
    print(evt.data.name) -- Jimmy
  end
end

--Set action listener
local function onSetResult(evt)
  if evt.isError then
    print(evt.error)
  else
    if evt.success then
      print('saved')
    end
  end
end

--Setting data
local function setData()
  --Set action
  profile:set({name="Jimmy", age=23}, onSetResult)
end

--Getting data
local function getData()
  --Get action
  profile:get(onResult)
end
```

---

# User Scope

A SkyTable, and its data, is scoped per user. When first initalizing the SkyTable client, you must provide a _username_ (usually an email address), and a _password_. These values are the responsibility of the developer to gather.

!!! note
    The _username_ and _password_ are never stored on the SkyTable server. Instead a unique key is generated to identify the user.

The _username_ and _password_ are passed to the __init__ API method:

```lua
local skytable = require("plugin.skytable")

skytable:init({
  user = "user@email.com",
  password = "<user-password>",
  ...
})
```

This feature allows your user to run your application on different devices, and have their data "synced" between sessions.

See the __[init](api/#init)__ API method for more details.

!!! warning
    If a _username_ and/or _password_ are changed, it is the responsibilty of the developer to __[delete](api/#delete)__ the old data, and initalize the new tables using the updated _username_ and _password_.

---

# Base Scope

You can also assign a _base scope_ to the SkyTable. This allows the saving of different data tables, per user, for multiple applications. A user can use the same _username_ and _password_ across all of your different applications.

!!! tip
    Even if you don't think you will have the same users on different applications, it is a best practice to assign a _base scope_ anyway. This future-proofs the SkyTable data.
  
The _base scope_ is assigned in the __[init](api/#init)__ Api method:

```lua
local skytable = require("plugin.skytable")

skytable:init({
  user = "user@email.com",
  password = "<user-password>",
  base = "app1",
  ...
})
```

Now when we want to access data from a different app (in this case "app2"), for the same user, we can do:

```lua
local skytable = require("plugin.skytable")

skytable:init({
  user = "user@email.com",
  password = "<user-password>",
  base = "app2", --different base scope
  ...
})
```

See the __[init](api/#init)__ API method for more details.

---

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

---

# Data Paths

Data paths allow you to "path" to a value in the SkyTable, both to __set__ and __get__ the underlying value. Data paths can be used in all of the value based API calls. This includes __get__, __set__, __delete__, and __keys__.

When calling any of the value methods _without_ a data path, a "root" path is implied. For example, let's assume the following data exists in a "profile" SkyTable:

```lua
{
  name = "Jim",
  age = 34,
  active = true,
  address = {
    street = "123 Main St.",
    city = "San Diego",
    state = "CA",
    zip = 92037
  },
  colors = {"red", "green", "blue"}
}
```

__Pathing with Get__

Then following call brings back the entire SkyTable (its "root" path):

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  local data = evt.data --the entire data table
  print(data.name) -- Jim
end

profile:get(onResult)
```

To gain access to an individual value in the SkyTable, we can supply a path:

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  local data = evt.data --holds "name" value
  print(data) -- Jim
end

profile:get("name", onResult)
```

To dive even deeper into the SkyTable, just path to the key, using a "dot" separator:

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  local data = evt.data --holds "city" value
  print(data) -- San Diego
end

profile:get("address.city", onResult)

```

If we wanted the entire "address" table:

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  local data = evt.data --entire address table
  print(address.state) -- CA
end

profile:get("address", onResult)
```

Or the "colors" table array:

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  local data = evt.data --entire colors array
  for i=1, #data.colors  do
    print(data.colors[i]) --red, green, blue
  end
end

profile:get("colors", onResult)
```

You can path as far as you need:

```lua
gear:get("set1.arms.def", onResult)
```

__Pathing with Set__

When using paths with the __set__ API call, the usage outlined above stays the same, with some implementation differences when setting a "root" path.

When first populating a SkyTable, you _must_ pass it a data table to start with. This can be as little as one property, or a whole predefined table. To create the inital SkyTable we do like so:

```lua
local profile = skytable:open("profile")

local function onSetResult(evt)
  if evt.success then
    print('saved')
  end
end

local data_tbl = 
{
  name = "Jim"
}

profile:set(data_tbl, onSetResult)
```

Once we have a data table saved to a SkyTable, we can set values on it at a later time by using a path:

```lua
local profile = skytable:open("profile")

local function onSetResult(evt)
  if evt.success then
    print('saved')
  end
end

profile:set("name", "Sally", onSetResult)
```

To add an "address" data table to our SkyTable, we can do:

```lua
local profile = skytable:open("profile")

local address = {
  street = "123 Main St.",
  city = "San Diego",
  state = "CA",
  zip = 92037
}

local function onSetResult(evt)
  if evt.success then
    print('saved')
  end
end

profile:set("address", address, onSetResult)
```

And to change a value in the "address" data table:

```lua
local profile = skytable:open("profile")

local function onSetResult(evt)
  if evt.success then
    print('saved')
  end
end

profile:set("address.city", "San Francisco", onSetResult)
```

Or adding an array table:

```lua
local profile = skytable:open("profile")

local function onSetResult(evt)
  if evt.success then
    print('saved')
  end
end

profile:set("colors", {"red","green","blue"}, onSetResult)
```

!!! important
    Once the "root" data table has been set for a SkyTable, you cannot overwrite it without specifying a flag.

To _overwrite_ a "root" data table in a SkyTable, you must pass a special flag. This is not required when creating the initial data.

```lua
profile:set(newDataTable, onSetResult, {flag="XX"})
```

This helps prevent overwriting your "root" data table by accident. Flags can also be used with data paths, but the pathed value will be overwritten on any __set__ call.

See __[Flags](#flags)__ for more details.

---

# Tags

When using a general listener (see __[Listeners](#listeners)__), you can mark an API call with a _tag_ to filter the response.

A _tag_ is added to a call using the __options__ parameter, which takes a table:

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  if not evt.isError then
    print(evt.error)
  else
    local data = evt.data
    if evt.tag == "get-name" then
      print(data) -- name string
    elseif evt.tag == "get-colors" then
      print(data[1]) -- colors table array
    end
  end
end

profile:get("name", onResult, {tag="get-name"})
profile:get("colors", onResult, {tag="get-colors"})
```

You can also use this method to create one main generalized response listener for both the __set__ call, as well as __get__, __delete__, and __keys__:

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  if not evt.isError then
    print(evt.error)
  else
    if evt.tag == "set-name" then
      if evt.success then
        print('name saved')
      end
    elseif evt.tag == "get-colors" then
      print(evt.data[1]) -- colors table array
    end
  end
end

profile:set("name", "Sam", onResult, {tag="set-name"})
profile:get("colors", onResult, {tag="get-colors"})
```

---

# Flags

When using the __set__ API call, you can supply a _flag_ to the __options__ parameter, as a table. 

__Flag Types__

|Name|Description|Type|
|----|-----------|----|
|NX|Set the path value only if the path ___does not___ exist.|String|
|XX|Set the path value only if the path ___does___ exist.|String|

```lua
local profile = skytable:open("profile")

local function onSetResult(evt)
  if evt.success then
    print('saved')
  end
end

--set the name only if the path exists.
profile:set("name", onSetResult, {flag="XX"})
```
!!! note
    When calling a __set__ without a flag, the value will be created if the path does not already exists, or replaced if it does.

---

# Expiry

When using the __set__ API call, you can also supply an _expiry_ to "expire" the SkyTable at a specified time. 

!!! warning
    Once a SkyTable has been removed via an expiry, it is no longer accessible.

__Expiry Properties__

|Name|Description|Type|
|----|-----------|----|
|seconds|The seconds until the SkyTable will be removed.|Number|

```lua
local profile = skytable:open("profile")

local onSetResult(evt)
  if evt.success then
    print('saved', 'expiry:', tostring(evt.expiry))
  end
end

--remove the SkyTable in 5 minutes
profile:set(data_tbl, onSetResult, {expiry=300})
```

!!! note
    When a path is updated using the __set__ API call, the expiry will be reset to its initial value. For example, if you set a value after 2 minutes on a 5 minute exipry, the expiry will be reset to 5 minutes.

!!! tip
    To automatically clean out inactive SkyTables, set a high expiry value.

---

# Delete

The __delete__ API call will remove a value at the given path.

!!! warning
    This method should be used with caution. ___When operating on the "root" path, the entire data table will be cleared___, and the path will no longer exist. Unlike __set__, with its [flag protection](#flags), a __delete__ method will do its job as long as the path exists.

To delete a value at the specified path:

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  if not evt.isError then
    if evt.data > 0 then
      print('name deleted from profile')
    end
  end
end

profile:delete("name", onResult)
```

!!! note
    A __delete__ event will contain a _data_ property with a number noting the value paths removed. Most often this will be a one (1). In the case of a failed attempt, or invalid path, the _data_ will contain the number zero (0).

---

# Keys

The __keys__ API call will return a tables keys in the SkyTable at the given path. Using our "profile" example, we can return all the keys at the "root" path like so:

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  if not evt.isError then
    local keys = evt.data
    --keys = {"name","age","active","address","colors"}
  end
end

profile:keys(onResult)
```

Using a path to a nested table:

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  if not evt.isError then
    local keys = evt.data
    --keys = {"street","city","state","zip"}
  end
end

profile:keys("address", onResult)
```

!!! note
    You can only access keys from data tables.

---

# Syncing Data

With the use of data paths (see __[Data Paths](#data-paths)__), you can __get__ and __set__ values from different levels of the SkyTable.

As a best practice, and to keep your data synced between client and server, it is best to create or cache a local data table first, make changes to that table, and then push it to the server. This helps keep your data in sync easier.

```lua
local profile_data = {}
local profile = skytable:open("profile")

local function onSetProfile(evt)
  if evt.isError then
    print(evt.error)
  else
    if evt.success then
      print('profile saved')
    end
  end
end

local function onGetProfile(evt)
  if evt.isError then
    print(evt.error)
  else
    --check if we have existing data
    if evt.data ~= nil then
      profile_data = evt.data
    else
      --there is no data table yet, create a default one
      profile_data = {
        name = "Tammy",
        age = 45
      }
      profile:set(profile_data, onSetProfile)
    end
  end
end

profile:get(onGetProfile)

```

Once you have the local data table, use it to manage the data, and push it back to the server after any changes:

```lua
profile_data.active = true
profile_data.colors = {"red","green","blue"}

profile:set(profile_data, onSetProfile, {flag="XX"})
```

!!! note
    When _replacing_ a "root" value (as above), you must specify the "XX" flag. See __[Flags](#flags)__.
