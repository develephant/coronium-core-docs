# Overview

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
local page = core.pages()

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

!!! tip
    To keep your template code from accidentally being viewed raw in the browser, be sure to name all template files with the __.tpl__ extension.

# Template Tags

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

---

# Looping Data

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

__Example Lua__

```lua
local page = core.pages()

local data =
{
  title = "Users",
  users =
  {
    { first="Jim", last="Bell" },
    { first="Dani", last="Bell" }
  }
}

local body = page.template('users.tpl', data)

page.response(body)
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

---

# Asynchrous Request

To check for an asynchrous request, you can use the __isAjax__ property of the pages object instance, and output the data in the required format.

__Example Lua__

```lua
-- cats.lua
local page = core.pages()

local cats =
{
  'Snookie','Fluffy','Yip'
}

if page.isAjax then
  --return JSON array
  local json = core.json.encode(cats)
  page.response(json, nil, page.JSON)
else
  --pass it to a template instead
  local body = page.template("cats.tpl", cats)
  page.response(body)
end
```

__Example HTML__

_Using JQuery_

```
...

<body>
  <div id='cats'>cats go here</div>

  <script>
    $.getJSON('https://my.coronium.com/cats', function(data) {
      $('cats').innerHTML = data.request;
    });
  </script>
</body>

...
```
