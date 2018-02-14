Make external HTTP network requests and retrieve the results.

### request

Send a network request to an external host address.

```lua
core.network.request(url, params)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for the network request.|_String_|__Y__|
|params|A table of options for the request. See __Params Table Keys__ below.|_Table_|__Y__|

__Params Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|method|The HTTP method for the request ("GET", "POST", etc.).|_String_|__Y__|
|headers|A table of headers to send with the request.|_Table_|__N__|
|body|A string body to send with a "POST" request.|_String_|__N__|
|ssl_verify|Verify SSL cert matches hostname.|_Boolean_|__N__|

__Returns__

On success, returns a __table__ with response keys (see below). Otherwise, __nil__ and an error.

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
  core.log(err)
end

core.log("body", resp.body)
core.log("status", resp.status)
```

### get

Convenience method for a "GET" request.

```lua
core.network.get(url[, headers])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for the "GET" request.|_String_|__Y__|
|headers|Optional headers to send with the request.|_Table_|__N__|

__Returns__

On success, returns a response object as a __table__ (see [network.request](#request)). Otherwise, __nil__ and an error.

__Example__

```lua
local resp, err = core.network.get("https://google.com")
if not resp then
  core.log(err)
end

core.log(resp.body)
```

### post

Convenience method for a "POST" request. 

```lua
core.network.post(url, body[, headers])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for the "POST" request.|_String_|__Y__|
|body|A string body to post to the endpoint.|_String_|__N__|
|headers|Optional headers to send with the request.|_Table_|__N__|

__Returns__

On success, returns a response object as a __table__ (see [network.request](#request)). Otherwise, __nil__ and an error.

__Example__

```lua
local body = "Here is some text I am posting."

local resp, err = core.network.post("https://post.com/submit", body)
if not resp then
  core.log(err)
end

core.log(resp.body)
```

See also: [Form Example](#form-example)

### getJson

Specialized method that sends a "GET" request to an endpoint that responds with JSON. 

```lua
core.network.getJson(url[, headers])
```

!!! info "Special Response"
    This method returns a Lua table with the decoded JSON response, not the JSON string.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for the network request.|_String_|__Y__|
|headers|Optional headers to send with the request.|_Table_|__N__|

__Returns__

On success, returns the decoded JSON result as a __table__. Otherwise, __nil__ and an error.

__Example__

```lua
local resp, err = core.network.getJson("https://getjson.com")

if not resp then
  core.log(err)
end

core.log(resp.some_key)
```

### postJson

Specialized method that sends a "POST" request to an endpoint that expects JSON.

```lua
core.network.postJson(url, tbl[, headers])
```

!!! info "Special Response"
    This method takes a Lua table and JSON-encodes it. Do not pass a raw JSON string.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|url|The url endpoint for to post the JSON data to.|_String_|__Y__|
|tbl|A Lua table of data to be JSON encoded.|_String_|__Y__|
|headers|Optional headers to send with the request.|_Table_|__N__|

__Returns__

On success, returns the result as a __string__. Otherwise, __nil__ and an error.

__Example__

```lua
local data = {
  name = "Tim",
  age = 34
}

local resp, err = core.network.postJson("https://postjson.com", data)

if not resp then
  core.log(err)
end

core.log(resp.body)
```

### pipeline

Run a sequential set of network requests against a specific host address.

```lua
core.network.pipeline(requests, url)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|requests|A table array of request tables. See __Request Table Keys__ below.|_Table_|__Y__|
|url|The url endpoint for to run the requests against.|_String_|__Y__|

__Request Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|path|The endpoint for the request. This is joined with the `url` provided.|_String_|__Y__|
|method|The HTTP method for the request. Defaults to "GET"|_String_|__N__|
|headers|A table of headers to send with the request.|_Table_|__N__|
|body|A string body to send with a "POST" request.|_String_|__N__|
|ssl_verify|Verify SSL cert matches hostname.|_Boolean_|__N__|

__Returns__

On success, a __table__ array of response tables (see below), or __nil__, and an error.

__Response Table Keys__

|Name|Description|Type|
|----|-----------|----|
|body|The response body returned by the request, if any.|_String_|
|headers|A table of response headers from the request.|_Table_|
|status|The HTTP response status of the request.|_Number_|

__Example__

```lua
local reqs = {
  {
    --defaults to "GET"
    path = "/page1.html"
  },
  {
    path = "/page2.html",
    method = "POST",
    body = "{\"name\":\"JSON\"}",
    headers = {
      ["Content-Type"] = "application/json"
    }
  }
}

local responses, err = core.network.pipeline(reqs, "http://somesite.com")

if not responses then
  core.log(err)
end

for r=1, #responses do
  if responses[r].status == 200 then
    core.log(responses[r].body)
  end
end
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