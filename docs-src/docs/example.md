# Demo

You can find the demo files here: [https://github.com/develephant/coronium-skytable-demo](https://github.com/develephant/coronium-skytable-demo)

!!! note "Screencast"
    Learn how to use the demo while viewing a screencast by [clicking here](https://www.youtube.com/watch?v=IZCRcva5lLM).

---

# Basic Get

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  if evt.isError then
    print(evt.error)
  else
    local tbl = evt.data --data table
  end
end

profile:get(onResult)

```

---

# Get with Path

```lua
local profile = skytable:open("profile")

local function onResult(evt)
  if evt.isError then
    print(evt.error)
  else
    print(evt.data) --will be the "name" value
  end
end

profile:get("name", onResult)

```

---

# Get with Tags

```lua
local profile = skytable:open("profile")
local location = skytable:open("location")

local function onResult(evt)
  if evt.isError then
    print(evt.error)
  else
    if evt.tag == "get-name" then
      print(evt.data) --will be the "name" value
    elseif evt.tag == "get-street" then
      print(evt.data) --will be the "street" value
    end
  end
end

profile:get("name", onResult, {tag="get-name"})
location:get("address.street", onResult, {tag="get-street"})

```

---

# Basic Set

```lua
local location = skytable:open("location")

local function onSetResult(evt)
  if evt.isError then
    print(evt.error)
  else
    print(evt.success)
  end
end

local locationData = 
{
  address = 
  {
    street = "123 Main St.",
    city = "San City",
    state = "Anywhere"
  }
}

location:set(locationData, onSetResult)
```

---

# Set with Path

```lua
local location = skytable:open("location")

local function onSetResult(evt)
  if evt.isError then
    print(evt.error)
  else
    print(evt.success)
  end
end

location:set("address.state", "Someplace", onSetResult)
```

---

# Set with Tags

```lua
local location = skytable:open("location")
local profile = skytable:open("profile")

local function onSetResult(evt)
  if evt.isError then
    print(evt.error)
  else
    if evt.tag == "set-location" then
      print("Set location", evt.success)
    elseif evt.tag == "set-profile-name" then
      print("Set profile", evt.success)
    end
  end
end

local locationData = 
{
  address = 
  {
    street = "123 Main St.",
    city = "San City",
    state = "Anywhere"
  }
}

location:set(locationData, onSetResult, {tag="set-location"})
profile:set("name", "Steve", onSetResult, {tag="set-profile-name"})
```

---

# Set with Flags

```lua
local location = skytable:open("location")

local function onSetResult(evt)
  if evt.isError then
    print(evt.error)
  else
    print(evt.success)
  end
end

local locationData = 
{
  address = 
  {
    street = "555 1st Ave.",
    city = "Citopia",
    state = "Bliss"
  }
}

location:set(locationData, onSetResult, {flag = "XX"})
```

# Set with Expiry

```lua
local location = skytable:open("location")

local function onSetResult(evt)
  if evt.isError then
    print(evt.error)
  else
    print(evt.success, evt.expiry)
  end
end

local locationData = 
{
  address = 
  {
    street = "555 1st Ave.",
    city = "Citopia",
    state = "Bliss"
  }
}

location:set(locationData, onSetResult, {expiry = 60})
```