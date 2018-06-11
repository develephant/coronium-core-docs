Provides methods to handle web based requests, and issue responses. For full usage instructions see the __[Usage](/server/modules/pages/usage/)__ section.

!!! tip "Screencasts Available"
    Get an overview of using the Pages API in a screencast format. __[Click here for Part I](/screencasts/#pages-api-part-i)__. __[Click here for Part II](/screencasts/#pages-api-part-ii)__.

### new

Creates a new __pages__ object instance.

```lua
core.pages.new( [request_opts] )
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|request_opts|Additional options for the page request.|_Table_|__N__|

__Request Options__

|Name|Description|Type|Default|
|----|-----------|----|-------|
|timeout|Timeout in milliseconds for the page request.|_Number_|`1000`|
|max_file_size|The maximum upload file size in bytes.|_Number_|`10240`|
|max_file_uploads|Maximum amount of uploaded files per request.|_Number_|`10`|

__Properties__

A __pages__ object instance may contain the following properties.

|Name|Description|Type|
|----|-----------|----|
|hasQuery|If the request contains a query string.|_Boolean_|
|hasForm|If the request body is a form.|_Boolean_|
|isGet|If the request is of type "GET".|_Boolean_|
|isPost|If the request is of type "POST".|_Boolean_|
|isAjax|If the request is an ajax style request.|_Boolean_|
|query|The request query string as key/value pairs.|_Table_|
|form|Contains the form (or JSON) data as key/value pairs.|_Table_|
|files|Contains file upload objects (see [File Uploads](/server/modules/pages/uploads/)).|_Table_|
|cookies|Contains any set cookie data as key/value pairs.|_Table_|
|headers|The request headers as key/value pairs.|_Table_|
|method|The request method. Will be "GET" or "POST".|_String_|
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

## Output

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
|content|The content-type to output to the client browser.|_[Const](/server/modules/pages/usage/#responses)_|__N__|
|status|Numerical http status code to output to the client browser.|_Number_|__N__|

!!! warning "Important"
    One this method is called, the response phase will end. You cannot issue a [redirect](#result) or [status](#status) after calling this method.

!!! info ""
    For more detailed examples see the __[Usage](/server/modules/pages/usage/)__ section.

__Example__

```lua
page.response("Hello There")
```

### render

A helper method that will render a [template](/server/modules/pages/api/#template) and then issue a page [response](/server/modules/pages/api/#response) as HTML (`text/html`).

```lua
<page-object>.render(tpl_path, tpl_values[, headers])
```

!!! warning "Important"
    __If you use this helper method, DO NOT use the `response` method.__

__Example__

```lua
page.render(tpl_path, tpl_values[, headers])
```

### renderJson

A helper method that will output JSON (`application/json`) as a page response. Used for AJAX style responses.

```lua
<page-object>.renderJson(data_tbl[, headers])
```

!!! warning "Important"
    __If you use this helper method, DO NOT use the `response` method.__

__Example__

```lua
page.renderJson(data_tbl[, headers])
```

### renderText

A helper method that will output plain text (`text/plain`) as a page response.

```lua
<page-object>.renderText(text[, headers])
```

!!! warning "Important"
    __If you use this helper method, DO NOT use the `response` method.__

__Example__

```lua
page.renderText("This is plain text output")
```

## Utilities

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

!!! info ""
    For detailed template usage information see the __[Templates](/server/modules/pages/templates/)__ section.

__Example__

```lua
local body = page.template("greeting.tpl", {greet="Hello!"})
```

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

!!! warning "Important"
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

!!! warning "Important"
    One this method is called, the response phase will end. You cannot issue a [response](#response) or [redirect](#result) after calling this method.

__Example__

```lua
--send `Unauthorized` status
page.status(401)
```

## Uploads

### saveFile

Save an uploaded file to the __files__ directory. On success, returns file information table, or `nil` and an error.

```lua
<page-object>.saveFile(fileObj, destPath[, unique])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|fileObj|A file object reference. See __[File Uploads](/server/modules/pages/uploads/)__.|_Object_|__Y__|
|destPath|Path to store the file relative to the __files__ directory.|_String_|__Y__|
|unique|Generate a unique file name for the file. Default: __false__|_Boolean_|__N__|

!!! tip "Public Files"
    If you plan on displaying uploaded images or allowing downloads of your files from the browser, make sure to save them to the _public_ directory.

__Example__

```lua
local info, err = page.saveFile(page.files.avatar, '/pix')
```

__File Info Keys__

|Key|Description|
|----|-----------|
|file|The base name the file was stored as.|
|type|The file [MIME](https://www.iana.org/assignments/media-types/media-types.xhtml) type. Example: 'image/png'.|
|size|The total file size in bytes.|
|path|The absolute file path where the file was stored.|
|dirpath|The absolute directory path where the file was stored.|

### discardFile

Clear the uploaded file from the temp directory. Use when you want to abort the actual storage of the file.

```lua
<page-object>.discardFile(fileObj)
```

!!! note "Usage Note"
    The __[`saveFile`](/server/modules/pages/api/#savefile)__ method automatically removes the uploaded file from the temp directory.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|fileObj|A file object reference. See __[File Uploads](/server/modules/pages/uploads/)__.|_Object_|__Y__|

__Example__

```lua
local success, err = page.discardFile(page.files.avatar)
```

## Cookies

### Retrieving cookies

To retrieve a set cookie value, use the `cookies` property of the `page` object.

__Example__

```lua
local userName = page.cookies.userName
```

### setCookie

Set a browser cookie key/value.

```lua
<page-object>.setCookie(key, value[, options])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|key|The cookie key name.|_String_|__Y__|
|value|The cookie key value.|_String_|__Y__|
|options|Additional options for the cookie.|_Table_|__N__|

__Cookie Options__

|Name|Description|Type|Default|
|----|-----------|----|-------|
|path|The path on which this cookie is active.|_String_|__/__|
|secure|Only serve this cookie when using HTTPS.|_Boolean_|`false`|
|httponly|Disable cookie from client-side access.|_Boolean_|`false`|
|expires|How many days until this cookie expires.|_Number_|`0`|

!!! note "Cookie Expiration"
    By default, cookies are cleared when the user closes the browser window using the cookie. Set the `expires` key to set a persistent cookie.

__Example__

```lua
page.setCookie("userName", "Timmy", {
  expires = 2 -- expires in two days
})
```

### clearCookie

Clear a browser cookie key/value.

```lua
<page-object>.clearCookie(key[, path])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|key|The cookie key name.|_String_|__Y__|
|path|The path on which this cookie is active.|_String_|__N__|

!!! note "Cookie Path"
    Most browsers require the cookie path to clear the cookie value if the cookie path has been set to something other than root (__/__).

__Example__

```lua
page.clearCookie("userName")
```

## Project APIs

### callApi

Call a method on a project api endpoint that was built using the __[core.api](/server/modules/api/#coreapi)__.

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