Provides methods to handle web based requests, and issue responses. For full usage instructions see the __[Usage](/pages-guide/usage/)__ section.

!!! warning "Important"
    __See the__ __[Configuration](/pages-guide/config/)__ __section before attempting to use pages.__

!!! tip "Screencasts Available"
    Get an overview of using the Pages API in a screencast format. __[Click here for Part I](/screencasts/#pages-api-part-i)__. __[Click here for Part II](/screencasts/#pages-api-part-ii)__.

### new

Creates a new __pages__ object instance.

```lua
core.pages.new()
```

__Parameters__

This method has no required parameters.

__Properties__

A __pages__ object instance may contain the following properties.

|Name|Description|Type|
|----|-----------|----|
|hasQuery|If the request contains a query string.|_Boolean_|
|hasForm|If the request body is a form.|_Boolean_|
|hasBody|If the request contains body data.|_Boolean_|
|isGet|If the request is of type "GET".|_Boolean_|
|isPost|If the request is of type "POST".|_Boolean_|
|isAjax|If the request is an ajax style request.|_Boolean_|
|query|The request query string as key/value pairs.|_Table_|
|form|Contains the form data as key/value pairs.|_Table_|
|body|If not a form, will contain string body, if any.|_String_|
|method|The request method. Will be "GET" or "POST".|_String_|
|headers|The request headers as key/value pairs.|_Table_|
|path|The uri request path.|_String_|


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

### response

Sends the configured response back to the client browser. 

```lua
<page-object>.response(body[, headers][, content][, status])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|body|The data to output to the client browser.|_String_|__Y__|
|headers|Additional headers to send to the client browser.|_Table_|__N__|
|content|The content-type to output to the client browser.|_[Const](#corepages)_|__N__|
|status|Numerical http status code to output to the client browser.|_Number_|__N__|

!!! info "Important"
    One this method is called, the response phase will end. You cannot issue a [redirect](#result) or [status](#status) after calling this method.

__Example__

```lua
page.response("Hello There")
```

!!! info ""
    For more detailed examples see the __[Usage](/pages-guide/usage/)__ section.

### template

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

!!! info ""
    For detailed template usage information see the __[Templates](/pages-guide/templates/)__ section.

### redirect

Redirect the client browser to a different location.

```lua
<page-object>.redirect(uri[, isPerm])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|uri|The address to redirect the client browser to.|_String_|__Y__|
|isPerm|Whether this is a premanent redirect. Default: __false__|_Boolean_|__N__|

!!! info "Important"
    One this method is called, the response phase will end. You cannot issue a [response](#response) or [status](#status) after calling this method.

!!! tip ""
    The uri can be a remote address, for example: _https://google.com_

__Example__

```lua
page.redirect("/anotherpage")
```

### status

End the response by sending an HTTP status code to the client browser.

```lua
<page-object>.status(code)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|code|HTTP numerical status code ([reference](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)).|_Number_|__Y__|

!!! info "Important"
    One this method is called, the response phase will end. You cannot issue a [response](#response) or [redirect](#result) after calling this method.

__Example__

```lua
--send `Unauthorized` status
page.status(401)
```

### callApi

Call a method on a project api endpoint that was built using the __[core.api](/server-modules/api/#coreapi)__.

```lua
<page-object>.callApi(project, action, data_params)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|project|The name of the api project.|_String_|__Y__|
|action|The name of the api method to call.|_String_|__Y__|
|data_params|Values to pass to the api method.|_Table_|__Y__|

__Example__

```lua
local result, err = page.callApi("default", "test", {greet="Hello!"})
```