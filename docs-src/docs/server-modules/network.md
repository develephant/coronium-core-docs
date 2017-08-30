# Overview

Make external HTTP network requests and retrieve the results.

__Example__

```lua
local request = cloud.network.new('google.com') --host only, no path
request:path('/end/point')
request:method(cloud.POST)
request:body(<JSON CONTENT>)
request:headers({
  ['Content-Type'] = 'application/json',
  -- add more headers
})

local result, err = request:result()
```

---

!!! note
    All of the network methods, except for __new__ and __request__ are getters and setters.

__Using getters and setters__

```lua
-- To get a value, omit any arguments
local host = request:host()

-- To set a an option
request:host('12.12.12.12')

-- Chain options
request:host('12.12.12.12'):port(8080)
local result, err = request:result()
```

---

## new

```lua
core.network.new( [host][, port] )
```

__Parameters__

|Name|Description|Type|Default|Required|
|----|-----------|----|-------|--------|
|host|The hostname to connect to.|_String_|'127.0.0.1'|__N__|
|port|The port number to connect with.|_Number_|80|__N__|

_The host and port can be set with the network instance methods as well._

__Returns__

A new request connection object.

__Example__

```lua
local request = core.network.new('123.123.123.123', 8080)
```

---

## host

```lua
request:host( hostname )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|hostname|The hostname to connect to.|_String_|__Y__|

__Returns__

The hostname if no value is passed.

__Example__

```lua
local hostname = request:hostname()

--set
request:hostname('13.13.13.13')
```

---

## port

```lua
request:port( port )
```

__Parameters__

|Name|Description|Type|Default|Required|
|----|-----------|----|-------|--------|
|port|The port to connect to.|_Number_|80|__Y__|

__Returns__

The port if no value is passed.

__Example__

```lua
local port = request:port()

--set
request:port(8080)
```

---

## path

```lua
request:path( path )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|path|The path to connect to.|_String_|__Y__|

__Returns__

The path if no value is passed.

__Example__

```lua
local path = request:path()

--set
request:path('/echo/test')
```

---

## header

```lua
request:header( name, value )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|name|The header name|_String_|__Y__|
|value|The header value.|_String_|__Y__|

__Returns__

The header value if only the name key is passed.

__Example__

```lua
local header = request:header('Content-Type')

--set
request:header('Content-Type','application/json')
```

---

## headers

```lua
request:headers( headers )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|headers|A table of request headers.|_Table_|__N__|

__Returns__

All headers if no arguments are passed.

__Example__

```lua
local header = request:headers()

--set
request:headers({
  'Content-Type' = 'application/json',
  'Host' = '12.12.12.12.'
})
```

!!! warning
    The __headers__ method will clear _all_ existing header assigments.

---

## method

```lua
request:method( method )
```

__Parameters__

|Name|Description|Default|Required|
|----|-----------|-------|--------|
|method|A HTTP method. Can use method constants (see below).|_core.POST_|__Y__|

__Returns__

The HTTP method name if no arguments are passed.

__Example__

```lua
local method = request:method()

--set
request:method(cloud.GET)
```

__Method Constants__

|Constant|Description|
|--------|-----------|
|core.POST|POST request method|
|core.GET|GET request method|
|core.DELETE|DELETE request method|
|core.UPDATE|UPDATE request method|

---

## timeout

```lua
request:timeout( timeout )
```

__Parameters__

|Name|Description|Type|Default|Required|
|----|-----------|----|-------|--------|
|timeout|Timeout in __milliseconds__.|_Number_|500|__Y__|

__Returns__

The timeout setting if no arguments are passed.

__Example__

```lua
local timeout = request:timeout()

--set
request:timeout(30)
```

---

## ssl_verify

```lua
request:ssl_verify( verify_flag )
```

__Parameters__

|Name|Description|Type|Default|Required|
|----|-----------|----|-------|--------|
|verify_flag|Enable or disable SSL verification.|_Boolean_|false|__N__|

__Returns__

The ssl_verify value, if no arguments are passed.

__Example__

```lua
local ssl_verify = request:ssl_verify()

--set
request:ssl_verify(true)
```

---

## body

```lua
request:body( body_data )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|body_data|Set or retrieve the request body.|_String_|__N__|

__Returns__

The body data value, if no arguments are passed.

__Example__

```lua
local body_data = request:body()

--set
request:body("Here is some body data")
```

---

## result

Compiles the options and makes the network request. Must be the last method called on a network request object.

```lua
request:result()
```

__Returns__

The __result__ if successful, otherwise __nil__ and __error__ message.

__Example__

```lua
local result, err = request:result()
```
