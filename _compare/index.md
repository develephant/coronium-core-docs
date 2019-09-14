# ![logo](imgs/logo256.png)

# __Coronium Core__ is an all-in-one Lua application server built for [Corona](https://coronalabs.com/) developers.

<h5><i class="fas fa-cloud-upload-alt"></i> <a href="https://develephant.github.io/coronium-core-docs/server/installation/digitalocean/">Click Here To Install Coronium Core Now!</a></h5>

## Core Features

<table>
<tr>
<td><i class="fas fa-magic"></i> Simple Lua Based API</td>
<td><i class="fas fa-plug"></i> Corona Client Plugin</td>
<td><i class="fas fa-child"></i> Quick & Easy Installation</td>
</tr>
<tr>
<td><i class="fas fa-desktop"></i> Browser Based Webmin</td>
<td><i class="fas fa-database"></i> MySQL Databases</td>
<td><i class="fas fa-leaf"></i> Mongo Databases</td>
</tr>
<tr>
<td><i class="fas fa-users"></i> User Management</td>
<td><i class="fas fa-chart-pie"></i> Application Analytics</td>
<td><i class="fas fa-images"></i> File Mangement</td>

</tr>
<tr>

<td><i class="fas fa-cogs"></i> Recurring Jobs Service</td>
<td><i class="far fa-file-code"></i> Dynamic Web Pages</td>
<td><i class="fas fa-code"></i> Customizable Server APIs</td>
</tr>
<tr>
<td><i class="fas fa-envelope"></i> Email Sending via Mailgun</td>
<td><i class="fas fa-rocket"></i> Powered by Nginx & LuaJIT</td>
<td><i class="fas fa-book"></i> Detailed Documentation</td>
</tr>
</table>

## MySQL Example

__Client-Side Only__

_Using the Corona Plugin:_

```lua
local core = require("plugin.coronium-core")

core.init({
  server = "https://my.coronium.server",
  key = "4057c8a8-0ab2-11e8-8f97-3edbd9b391ed"
})

local function onResponse(evt)
  if evt.error then
    print(evt.error)
  else
    print(evt.result.name)
  end
end

core.mysql.selectOne({
  db = "products",
  tbl = "toys",
  where = { name = "red car" }
}, onResponse)
```

__Server-Side API__

_A server-side API module:_

```lua
local api = core.api()

function api.getCars(input)
  local res, err = core.mysql.selectOne("products", {
    tbl = "toys",
    where = { name = input.name }
  })

  if not res then
    return core.error(err)
  end

  return res
end

return api
```

_Calling from the client-side:_

```lua
--== Initialization (see above)

local function onResponse(evt)
  if evt.error then
    print(evt.error)
  else
    print(evt.result.name)
  end
end

core.api.getCars({name="red car"}, onResponse)
```

## Overview

Extend your applications into the cloud with Mongo and MySQL databases, user management, analytics, recurring jobs, secure file transfers and storage, dynamic public facing web pages, email, and more; all scripted using a ridiculously simple Lua based API.

__Coronium Core__ includes a fully visual browser based "Webmin" to view statistics, app analytics, work with users, manage API projects and databases, create recurring jobs, monitor your system, edit code with a robust code editor, and more.

Use the __[Coronium Core Corona plugin](https://marketplace.coronalabs.com/plugin/coronium-core)__ for most tasks without ever writing a line of code on the server. When you need to take it to the next level, create custom server-side APIs with Lua for complex data driven applications.

With the __Coronium Core__ all-in-one server, you own and control your data, with an easy to use custom built solution for [Corona](https://coronalabs.com) developers.

<h3><i class="fas fa-cloud-upload-alt"></i> <a href="https://develephant.github.io/coronium-core-docs/server/installation/digitalocean/">Click Here to Get Started Installing Coronium Core!</a></h3>
