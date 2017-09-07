# Overview

Provides methods to handle web based requests, and issue responses. For full usage instructions see the __[Usage](/pages-guide/usage/)__ section.

---

## core.pages

Creates an instance of the __pages__ object.

```lua
core.pages()
```

__Parameters__

This method has no parameters.

__Properties__

The __pages__ object contains the [response]() and [template]() methods below, as well as, the following properties.

|Name|Description|Type|
|----|-----------|----|
|method|The request method. Will be "GET" or "POST".|_String_|
|headers|The request headers as key/value pairs.|_Table_|
|query|The request query string as key/value pairs.|_Table_|
|form|If posted, will contain form key/value pairs.|_Table_|
|body|If the method is POST, will contain the body, if any.|_String_|
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
local pages = core.pages()
```

!!! note
    For more detailed examples see the __[Usage](/pages-guide/usage/)__ section.

---

## response | resp

Sends the configured response back to the client browser. 

```lua
pages.response(body[, headers][, content][, status])
```

!!! important
    This should be the last method in your page file.

__Parameters__



__Example__

```lua
pages.response("Hello There")
```

---

## template | tpl

Compiles a template file with the supplied values. Returns __string__ body, or nil and error.

```lua
pages.template(tpl_file, tpl_values)
```

__Parameters__



__Example__

```lua
local body = pages.template("greeting.tpl", {greet="Hello!"})
```

!!! note
    For detailed template usage information see the __[Templates](/pages-guide/templates/)__ section.
