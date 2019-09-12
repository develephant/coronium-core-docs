Pages allow you to display and collect data using your Coronium Core server. You can display simple static web pages, or serve [dynamic pages](#dynamic-pages) using Lua.

Only the GET and POST HTTP methods are supported for server requests.

!!! tldr "Special Note"
    The __Pages__ module is meant to supplement the core functionality of the application server. It is not designed to serve entire websites.

## Pages Directory

All pages and assets reside in the __/home/coronium/pages__ directory, which is the "root" directory for all web based requests. Log in via SFTP to manage your page files.

## Dynamic Pages

To create a dynamic web page, name the file with the __.lua__ extension and use the __[new](/server/modules/pages/api/#new)__ method to create a pages object instance. The browser path will be the path relative to the pages directory. Call the Lua file by addressing it by name, without the .lua extension.

__Example Paths__

File path: ___/home/coronium/pages/users/show.lua___

Browser path: ___https://my.coronium.com/users/show___

__Example Lua__

```lua
--users/show.lua
local page = core.pages.new()

local body = 
[[
  <html>
    <body>
      <h1>Hello Pages!</h1>
    </body>
  </html>
]]

page.response(body)

```

## Responses

Output responses to the client web browser by using the __[response](/server/modules/pages/api/#response)__ method of the pages object instance. The default response type is html, but you can also respond with json, or plain text content types.


### HTML

To return content as html (default), pass a string body to the __response__ method.

```lua
local page = core.pages.new()

local body = 
[[
  <html>
    <body>
      <h1>Hello Pages!</h1>
    </body>
  </html>
]]

page.response(body)
```

See also: __[Templates](/server/modules/pages/templates/)__

### JSON

To return content as json, pass the __JSON__ constant to the __content__ response parameter.

```lua
local page = core.pages.new()

--use a table for easy construction
local data = {
  name = "Dani",
  age = 23
}

local body = core.json.encode(data)

page.response(body, nil, page.JSON)
```

### TEXT

To return content as text, pass the __TEXT__ constant to the __content__ response parameter.

```lua
local page = core.pages.new()

local body = "This is some plain text"

page.response(body, nil, page.TEXT)
```

## Queries

To access properties of a query string, use the __query__ property of the pages object instance. The __query__ is a table with name/value pairs.

__Example Path__

___/mypage?name=Tim&age=42___

__Example Lua__

```lua
--mypage.lua
local page = core.pages.new()

local query_tbl = page.query

print(query_tbl.name) --Tim
print(query_tbl.age) --42

--Loop over query name/values
for name, val in pairs(query_tbl) do
  print(name.."="..val)
end

...
```

## Forms

To access properties of a posted form, use the __form__ property of the pages object instance. The form is a __table__ with name/value pairs.

__Example Form__

```html
<form>
  First name:<br>
  <input type="text" name="firstname"><br>
  Last name:<br>
  <input type="text" name="lastname">
</form>
```

__Example Lua__

```lua
--mypage.lua
local page = core.pages.new()

local form_tbl = page.form

local first = form.firstname
local last = form.lastname

print(first.." "..last)

--Loop over form name/values
for name, val in pairs(form) do
  print(name.."="..val)
end

...
```

## Headers

To access the request headers, use the __headers__ property of the pages object instance. The headers are in a __table__ as name/value pairs.

__Example__

```lua
local page = core.pages.new()

local headers = page.headers

--print "Host" header
print(headers["Host"])

--Loop over headers name/values
for name, val in pairs(headers) do
  print(name.."="..val)
end

...
```

## Core Modules

You can use any of the core server modules in dynamic pages.

__Example Template__

```html
<html>
  <body>
    <h1>{{ name }}</h1>
    <p>{{ age }}</p>
    <p>{{ _id }}</p>
  </body>
</html>
```

__Example Lua__

```lua
local page = core.pages.new()

local users = core.data("users")
local doc = users:get("id1234")

local body = page.template("user.tpl", doc)

page.response(body)
```

## Files Directory

Files uploaded to the __[files/public](/server/guide/usage/#public-directory)__ directory using the __[Files](/client/modules/files/)__ module can be displayed using page templates. When referencing a file in a template, the path will be checked in the __pages__ directory first. If no file is found, then the __files/public__ directory path will be checked.