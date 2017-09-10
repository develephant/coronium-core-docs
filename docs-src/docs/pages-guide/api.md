# Overview

Provides methods to handle web based requests, and issue responses. For full usage instructions see the __[Usage](/pages-guide/usage/)__ section.

!!! important
    See the __[Configuration](/pages-guide/config/)__ section before attempting to use pages.

---

## new

Creates a new __pages__ object instance.

```lua
core.pages.new()
```

__Parameters__

This method has no required parameters.

__Properties__

A __pages__ object instance contains the [response](#response-resp) and [template](#template-tpl) methods below, as well as, the following properties.

|Name|Description|Type|
|----|-----------|----|
|method|The request method. Will be "GET" or "POST".|_String_|
|headers|The request headers as key/value pairs.|_Table_|
|query|The request query string as key/value pairs.|_Table_|
|form|If posted, will contain form key/value pairs.|_Table_|
|isAjax|If the request is an ajax style request.|_Boolean_|

__Constants__

The __pages__ object has the following constants available.

|Name|Description|Value|
|----|-----------|----|
|GET|The GET HTTP method.|"GET"|
|POST|The POST HTTP method.|"POST"|
|HTML|The HTML content type.|"text/html"|
|JSON|The JSON content type.|"application/json"|
|TEXT|The plain text content type.|"text/plain"|

__Example__

```lua
local page = core.pages.new()
```

_Raw Request Post Body_

In the rare case that you need access to the raw request body, pass __true__ to the __core.pages__ method. The body is then available on the __body__ property of the pages object instance. Beware that this does take up additional memory per request, and should only be used if you specifically need it.

```lua
local page = core.pages.new(true)
local raw_body = page.body
```

---

## response | resp

Sends the configured response back to the client browser. 

```lua
<page-object>.response(body[, headers][, content][, status])
```

!!! important
    This should be the last method in your page file.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|body|The data to output to the client browser.|_String_|__Y__|
|headers|Additional headers to send to the client browser.|_Table_|__N__|
|content|The content-type to output to the client browser.|_[Const](#corepages)_|__N__|
|status|Numerical http status code to output to the client browser.|_Number_|__N__|

__Example__

```lua
page.response("Hello There")
```

!!! note
    For more detailed examples see the __[Usage](/pages-guide/usage/)__ section.

---

## template | tpl

Compiles a template file with the supplied values. Returns __string__ body, or nil and error.

```lua
<page-object>.template(tpl_file, tpl_values)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tpl_file|The template file name.|_String_|__Y__|
|tpl_values|Values for the template.|_Table_|__Y__|

__Example__

```lua
local body = page.template("greeting.tpl", {greet="Hello!"})
```

!!! note
    For detailed template usage information see the __[Templates](/pages-guide/templates/)__ section.
