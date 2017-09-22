Make external HTTP network requests and retrieve the results.

### request

Send a network request. On success, returns a __table__ with response keys (see below). Otherwise, __nil__ and an error.

```lua
core.network.request(url, params)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for the network request.|_String_|__Y__|
|params|A table of options for the request.|_Table_|__Y__|

__Params Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|method|The HTTP method for the request ("GET", "POST", etc.).|_String_|__Y__|
|headers|A table of headers to send with request.|_Table_|__N__|
|body|A string body to send with request.|_String_|__N__|
|ssl_verify|Verify SSL cert matches hostname.|_Boolean_|__N__|

__Response Table Keys__

|Name|Description|Type|
|----|-----------|----|
|body|The response body returned by the request, if any.|_String_|
|headers|A table of response headers from the request.|_Table_|
|status|The HTTP response status of the request.|_Number_|

__Example__

```lua
local resp, err = core.network.request("https://google.com", {
  method = "POST",
  body = "{\"name\":\"JSON\"}",
  headers = {
    ["Content-Type"] = "application/json"
  }
})

if not resp then
  print(err)
end

print("body", resp.body)
print("status", resp.status)
```

### get

Convenience method for a "GET" request. On success, returns response body as a __string__. Otherwise, __nil__ and an error.

```lua
core.network.get(url[, headers])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for the "GET" request.|_String_|__Y__|
|headers|Optional headers to send with the request.|_Table_|__N__|

__Example__

```lua
local resp, err = core.network.get("https://google.com")
if not resp then
  print(err)
end

print(resp)
```

### post

Convenience method for a "POST" request. On success, returns response body as a __string__. Otherwise, __nil__ and an error.

```lua
core.network.post(url, body[, headers])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for the "POST" request.|_String_|__Y__|
|body|A string body to post to the endpoint.|_String_|__N__|
|headers|Optional headers to send with the request.|_Table_|__N__|

__Example__

```lua
local body = "Here is some text I am posting."

local resp, err = core.network.post("https://post.com/submit", body)
if not resp then
  print(err)
end

print(resp)
```

See also: [Form Example](#form-example)

### getJson

Specialized method that sends a "GET" request to an endpoint that responds with JSON. On success, returns the decoded JSON result as a __table__. Otherwise, __nil__ and an error.

!!! note "Usage Note"
    This method returns a Lua table with the decoded JSON response, not the JSON string.

```lua
core.network.getJson(url[, headers])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for the network request.|_String_|__Y__|
|headers|Optional headers to send with the request.|_Table_|__N__|

__Example__

```lua
local resp, err = core.network.getJson("https://getjson.com")

if not resp then
  print(err)
end

print(resp.some_key)
```

### postJson

Specialized method that sends a "POST" request to an endpoint that expects JSON. On success, returns the result as a __string__. Otherwise, __nil__ and an error.

!!! note "Usage Note"
    This method takes a Lua table and JSON-encodes it. Do not pass a raw JSON string.

```lua
core.network.postJson(url, tbl[, headers])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for to post the JSON data to.|_String_|__Y__|
|tbl|A Lua table of data to be JSON encoded.|_String_|__Y__|
|headers|Optional headers to send with the request.|_Table_|__N__|

__Example__

```lua
local data = {
  name = "Tim",
  age = 34
}

local resp, err = core.network.postJson("https://postjson.com", data)

if not resp then
  print(err)
end

print(resp)
```

### encode

Encode a table for use as post or query arguments.

```lua
core.network.encode(tbl)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tbl|A table with key/value pairs.|_Table_|__Y__|

#### Form Example

```lua
local form = core.network.encode({
  firstname = "Sally",
  lastname = "Jones"
})

local resp, err = core.network.post("http://post.com/submit", form)
...
```

#### Query Example

```lua
local args = core.network.encode({
  page = 1,
  perpage = 10
})

local url = "http://page.com?"..args

local resp, err = core.network.get(url)
...
```