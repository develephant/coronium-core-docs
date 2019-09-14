A template is simply an HTML file laced with template tags that will be filled in by values dynamically. 

Template files are stored relative to the file that will be using them. The file referencing the template will be the .lua pages file.

__Example Template__

_Stored in /home/coronium/pages/users/show.tpl_

```html
<html>
  <head>
    <title>{{ title }}</title>
  </head>
  <body>
    <p>{{ message }}</p>
  </body>
</html>
```

__Example Lua__

_Stored in /home/coronium/pages/users/show.lua_

```lua
local page = core.pages.new()

local data = 
{
  title = "Welcome!",
  message = "You are now using templates."
}

local body = page.template("show.tpl", data)

page.response(body)
```

__Browser Location__

_Path: https://my.coronium.com/users/show_

_See also:_ __[page.render](/server/modules/pages/api/#render)__

!!! tip
    To keep your template code from accidentally being viewed raw in the browser, be sure to name all template files with the __.tpl__ extension.

## Template Tags

__Escaped Value__

```
{{ expression }}
```

__Plain Value__

```
{* expression *}
```

__Lua Code__

```
{% code here %}
```

__Include__

```
{(other.tpl)}
```

__Raw Block__

```
{-raw-}non-interpolated text{-raw-}
```

## Looping Data

Template loops allow you to "loop" over data and generate the output.

_A Loop:_

```
{% for idx, item in ipairs(items) do %}
  <div>{{ item }} is item number {{ idx }}</div>
{% end %}
```

__Example Template__

```
<!-- users.tpl -->
<html>
<head>
  <title>{{ title }}</title>
</head>
<body>
  <div class="container">
    <ul class="user-list">
      {% for _, user in ipairs( users ) do %}
      <li class="user">{{ user.first }} {{ user.last }}</li>
      {% end %}
    </ul>
  </div>
</body>
</html>
```

__Example Page Lua__

```lua
local page = core.pages.new()

local data =
{
  title = "Users",
  users =
  {
    { first="Jim", last="Bell" },
    { first="Dani", last="Bell" }
  }
}

page.render('/users.tpl', data)
```

__Output__

```
<html>
<head>
  <title>Users</title>
</head>
<body>
  <div class="container">
    <ul class="user-list">
      <li class="user">Jim Bell</li>
      <li class="user">Dani Bell</li>
    </ul>
  </div>
</body>
</html>
```

## Asynchrous Request

To check for an asynchrous request, you can use the __isAjax__ property of the pages object instance, and output the data in the required format.

__Example Page Lua__

```lua
--cat.lua
local page = core.pages.new()

local cat = {
  name = 'Snookie'
}

local function renderPage()
  if page.isAjax then
    --return as JSON
    page.renderJson( cat )
  else
    --or render template
    page.render( '/cat.tpl', cat )
  end
end

--incoming
if page.isGet then
  renderPage()
else
  page.status(501)
end
```

__Example Template__

Endpoint: _http(s)://my.coronium.com/cat_

```html
<!-- cat.tpl -->
Cat: {{ name }}
```

__Example HTML__

Endpoint: _http(s)://my.coronium.com/cat.html_

Using JQuery

```html
<!-- cat.html -->
<html>
	<head>
    <script src="https://code.jquery.com/jquery-3.3.1.min.js" crossorigin="anonymous"></script>
	</head>
	<body>
    <div id="cat">cat name goes here</div>
    <script>
      $.getJSON("/cat", function(data) {
        $('#cat').text("Cat: "+data.name);
      });
    </script>
  </body>
</html>
```

__Output__

```html
Cat: Snookie
```