# Overview

Pages allow you to display and collect data using your Coronium Core server. You can display simple static web pages, or serve [dynamic pages](#dynamic-pages) using Lua.

Only the GET and POST HTTP methods are supported for server requests.

!!! note
    The Coronium Pages module is meant to supplement the core functionality of the application server. It is not designed to serve entire websites.

## Pages Directory

All pages and assets reside in the __/home/coronium/pages__ directory, which is the "root" directory for all web based requests. Log in via SFTP to manage your page files.

## Dynamic Pages

To create a dynamic web page, name the file with the __.lua__ extension and use the __[core.pages](/pages-guide/api/#corepages)__ object. The browser path will be the path relative to the pages directory. Call the Lua file by addressing it by name, without the .lua extension.

__Example Paths__

File path: ___/home/coronium/pages/users/show.lua___

Browser path: ___https://my.coronium.com/users/show___

__Example Lua__

```lua
--users/show.lua
local pages = core.pages()

local body = 
[[
  <html>
    <body>
      <h1>Hello Pages!</h1>
    </body>
  </html>
]]

pages.response(body)

```

---

# Responses

Output responses to the client web browser by using the __[pages.response]()__ method. The default response type is html, but you can also respond with json, or plain text content types.


## HTML

To return content as html (default), pass a string body to the __response__ method.

```lua
local pages = core.pages()

local body = 
[[
  <html>
    <body>
      <h1>Hello Pages!</h1>
    </body>
  </html>
]]

pages.response(body)
```

See also: __[Templates](/pages-guide/templates/)__

---

## JSON

To return content as json, pass the __pages.JSON__ constant to the __content__ response parameter.

```lua
local pages = core.pages()

--use a table for easy construction
local data = {
  name = "Dani",
  age = 23
}

local body = core.json.encode(data)

pages.response(body, nil, pages.JSON)
```

---

## TEXT

To return content as text, pass the __pages.TEXT__ constant to the __content__ response parameter.

```lua
local pages = core.pages()

local body = "This is some plain text"

pages.response(body, nil, pages.TEXT)
```

---

# Queries

To access properties of a query string, use the __pages.query__ property. The __query__ is a table with name/value pairs.

__Example Path__

___/mypage?name=Tim&age=42___

__Example Lua__

```lua
--mypage.lua
local pages = core.pages()

local query_tbl = pages.query

print(query_tbl.name) --Tim
print(query_tbl.age) --42

--Loop over query name/values
for name, val in pairs(query_tbl) do
  print(name.."="..val)
end

...
```

---

# Forms

To access properties of a posted form, use the __pages.form__ property. The form is a __table__ with name/value pairs.

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
local pages = core.pages()

local form_tbl = pages.form

local first = form.firstname
local last = form.lastname

print(first.." "..last)

--Loop over form name/values
for name, val in pairs(form) do
  print(name.."="..val)
end

...
```

---

# Headers

To access the request headers, use the __pages.headers__ property. The headers are in a __table__ as name/value pairs.

__Example__

```lua
local pages = core.pages()

local headers = pages.headers

--print "Host" header
print(headers["Host"])

--Loop over headers name/values
for name, val in pairs(headers) do
  print(name.."="..val)
end

...
```

---

# Core Modules

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
local pages = core.pages()

local users = core.data("users")
local doc = users:get("id1234")

local body = pages.template("user.tpl", doc)

pages.response(body)
```