Pages allow you to display and collect data using your Coronium Core server. You can display simple static web pages (.html), or serve [dynamic pages](#dynamic-pages) using Lua.

!!! tldr "Special Note"
    While the __Pages__ module is capable of serving up complex websites, it is generally meant to supplement the core functionality of the application server.

## Pages Directory

All pages and assets reside in the __/home/coronium/pages__ directory, which is the "root" directory for all web based requests. Log in via SFTP to manage your page files.

## Dynamic Pages

To create a dynamic web page, name the file with the __.lua__ extension and use the __[new](/server/modules/pages/api/#new)__ method to create a pages object instance. The browser path will be the path relative to the pages directory. 

Call the Page file by addressing it by name, without the .lua extension.

__Example Paths__

File path: ___/home/coronium/pages/users/show.lua___

Browser path: ___https://my.coronium.com/users/show___

## Requests

The Pages module supports both GET and POST requests. In most cases you should set up your page to handle both types of request. The following example demonstrates a common page request pattern:

```lua
-- mypage.lua
local page = core.pages.new()

-- GET handler
local function processGet()
  ...
end

-- POST handler
local function processPost()
  ...
end

--# Incoming Request
if page.isGet then
  -- GET request
  processGet()
elseif page.isPost then
  -- POST request
  processPost()
else
  -- Method not supported
  page.status(501)
end

```

## Responses

Output responses to the client web browser by using the various "output" methods of the pages object instance. The available response types are HTML, JSON, or plain text.


### HTML

To return content as html (`text/html`), pass a string body to the __[response](/server/modules/pages/api/#response)__ method.

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

_See also:_ __[Templates](/server/modules/pages/templates/)__

### JSON

To return content as JSON (`application/json`), use the __[renderJson](/server/modules/pages/api/#renderjson)__ method.

```lua
local page = core.pages.new()

--Uses a table for easy JSON construction
local data = {
  name = "Dani",
  age = 23
}

page.renderJson(data)
```

_See also:_ __[page.response](/server/modules/pages/api/#response)__

### TEXT

To return content as plain text (`text/plain`), use the __[renderText](/server/modules/pages/api/#rendertext)__ method.

```lua
local page = core.pages.new()

local text = "This is some plain text"

page.renderText(text)
```

_See also:_ __[page.response](/server/modules/pages/api/#response)__

## Queries

To access properties of a query string, use the __query__ property of the pages object instance. The __query__ is a table with name/value pairs.

__Example Path__

__/mypage?name=Tim&age=42__

__Example Lua__

```lua
--mypage.lua
local page = core.pages.new()

local function processPage()
  print(page.query.name) --Tim
  print(page.query.age) --42

  --Loop over query name/values
  for name, val in pairs(page.query) do
    print(name.."="..val)
  end
end

--# Incoming
if page.isGet then
  processPage()
else
  page.status(501)
end
```

## Forms

To access properties of a posted form, use the __form__ property of the pages object instance. The form is a __table__ with name/value pairs.

__Example Form__

```html
<form action="" method="POST">
  <input name="firstname" id="firstname">
  <input name="lastname" id="lastname">
  <button type="submit">Submit</button>
</form>
```

__Example Lua__

```lua
--myform.lua
local page = core.pages.new()

local function processForm()
  local first = page.form.firstname
  local last = page.form.lastname

  print(first.." "..last)

  --Loop over form name/values
  for name, val in pairs(page.form) do
    print(name.."="..val)
  end
end

--# Incoming
if page.isPost then
  processForm()
else
  page.status(501)
end
```

## Uploads

To access file uploads, use the __files__ property of the pages object instance.

__Example Upload Form__

```html
<!-- upload.html -->
<form action="" method="POST" enctype="multipart/form-data">
  <input type="file" name="avatar" id="avatar">
  <button type="submit">Upload</button>
</form>
```

__Example Lua__

```lua
-- upload.lua
local page = core.pages.new()

local function processUpload()
  local avatarObj = page.files.avatar
  page.saveFile(avatarObj, '/avatars')
  page.response("Done")
end

--# Incoming
if page.isPost then
  processUpload()
else
  page.status(501)
end
```

See the __[File Uploads](/server/modules/pages/uploads/)__ section.

## Cookies

See __[Cookies](/server/modules/pages/api/#cookies)__ in the Pages API documentation.

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
```

## Core Modules

You can use any of the core server modules in dynamic pages.

__Example Template__

```html
<!-- user.tpl -->
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
-- user.lua
local page = core.pages.new()

local function processPage()
  local users = core.data("users")
  local doc = users:get("id1234")

  page.render('/user.tpl', doc)
end

--# Incoming
if page.isGet then
  processPage()
else
  page.status(501)
end

```

## Files Directory

Files uploaded to the __[files/public](/server/guide/usage/#public-directory)__ directory using the __[Files](/client/modules/files/)__ module can be displayed using page templates. When referencing a file in a template, the path will be checked in the __pages__ directory first. If no file is found, then the __files/public__ directory path will be checked.