Here you can find various helpers available in the root __core__ namespace.

### log

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

### sf (string format)

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

### trim

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

### split

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

### json

JSON encoding and decoding.

```lua
local json = core.json
```

_Encode_

```lua
local str = json.encode(tbl)
```

_Decode_

```lua
local tbl = json.decode(str)
```

### json_safe

JSON encoding and decoding with error handling.

```lua
local json = core.json_safe
```

_Encode safe_

```lua
local str, err = json.encode(tbl)
```

_Decode safe_

```lua
local tbl, err = json.decode(str)
```

### uuid

Generates a universally unique id.

__Example__

```lua
local uuid = core.uuid()

-- `uuid` will contain something like "1f0af2fa-8b06-4605-bace-e13a85aa36d5"
```

### countryCode

Two letter country code based on the request IP. Can only be called in a __core.api__ method.

__Example__

```lua
local code = core.countryCode()

-- `code` will contain something like "US" or "EU"
```

### callApi

Call a method on a custom api endpoint built using the __[core.api](/server-modules/api/#coreapi)__. This method allows you to call other project api methods from within a different project api.

```lua
core.callApi(project, action, data_params)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|project|The name of the api project.|_String_|__Y__|
|action|The name of the api method to call.|_String_|__Y__|
|data_params|Values to pass to the api method.|_Table_|__Y__|

__Example__

```lua
local result, err = core.callApi("default", "test", {greet="Hello!"})
```