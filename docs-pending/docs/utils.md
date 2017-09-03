# Overview

Here you can find various helpers available in the root __core__ namespace.

---

## log

Prints a string message to the __/usr/local/coronium/logs/coronium.log__ file.

```lua
core.log( message )
```

__Parameters__

|Name|Description|Requried|
|----|-----------|--------|
|message|The message string to log.|__Y__|

__Example__

```lua
core.log('something to log')
```

---

## sf (string format)

Format a string based on token replacment. This method shares the same attributes as the Lua string.format method.

```lua
core.sf( str_template, values )
```

__Parameters__

|Name|Description|Requried|
|----|-----------|--------|
|str_template|The string containing replacment tokens.|__Y__|
|values|A list of values for token replacment.|__Y__|

__Example__

```lua
local username = "Dave"
local formatted_str = core.sf("Hello, %s!", username)

-- formatted_str = "Hello, Dave!"
```

__Notes__

Some common token types:

|Token|Description|
|-----|-----------|
|%s|Used as a string replacment, any other type will error.|
|%d|Used as a number replacment, any other type will error.|

You can mix tokens, as well as have multiples:

```lua
local str = core.sf("Ordered %d %s with %s.", 2, 'burgers', 'cheese')
-- `str` contains "Ordered 2 burgers with cheese."
```

!!! tip
    The __sf__ method is useful for creating SQL queries, as well as other type-safe replacments.

---

## trim

Removes empty space from the start and end of a string, if any.

```lua
core.trim( string )
```

__Parameters__

|Name|Description|Requried|
|----|-----------|--------|
|string|The string to trim.|__Y__|

__Example__

```lua
local trimmed_str = core.trim("  I could use a trim  ")

-- trimmed_str = "I could use a trim"
```

---

## split

Converts a delimited string into a Lua table array.

```lua
core.split( string[, delimiter ] )
```

__Parameters__

|Name|Description|Default|Requried|
|----|-----------|-------|--------|
|string|The delimited string to split.|_None_|Y|
|delimiter|The delimiter to split the string with.|_Comma_ ( , )|N|

__Example__

```lua
local tbl_array = core.split("Red,Green,Blue")

-- `tbl_array` contains {"Red","Green","Blue"}
```

Using a custom delimiter:

```lua
local tbl_array = core.split("User:2001:Storage", ":")

-- `tbl_array` contains {"User","2001","Storage"}
```

---

## uuid

Generates a universally unique id.

__Example__

```lua
local uuid = core.uuid()

-- `uuid` will contain something like "1f0af2fa-8b06-4605-bace-e13a85aa36d5"
```

---

## tblmerge

Merge a series of tables, with optional default inputs. Last value wins.

```lua
core.tblmerge( default_vals, tbl_obj_1[, tbl_obj_2[, ...]] )
```

!!! note
    This method only works on the root table keys.

__Parameters__

|Name|Description|Type|Requried|
|----|-----------|----|--------|
|default_vals|A keyed table of default values. These values can be overridden.|_Table_|__Y__|
|tbl_obj_N|A keyed table of values. Will override any existing default_vals. You can add as many additional tables as needed.|_Table_|__Y__|

__Returns__

Merged table.

__Usage__

```lua
--some "defaults"
local defaults =
{
  color = "blue",
  points = 0
}

--a "user config"
local user_config =
{
  color = "red"
}

--some other meta
local meta =
{
  last_score = 200,
  fav_avatar = "orange",
  points = 10
}

--merge tables
local merged_tbl = core.tblmerge( defaults, user_config, meta )
```

The merged table result:

```lua
{
  color = "red",
  points = 10,
  last_score = 200,
  fav_avatar = "orange"
}
```
