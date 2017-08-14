# coronium-core-docs
Documentation for Coronium Core

# Internal Docs

## Directories

### Core

`/usr/local/coronium`

Holds all the coronium server app files and binaries.

### API

`/usr/local/coronium/lualib/api`

Holds the api files for external access. These are basically internal "projects" that can be accessed with the client.

### Plugins

`/usr/local/coronium/lualib/coronium/plugins`

Holds plugins that extend coronium core library. Plugins are enabled in the `coronium.lua` file.

Note: _Plugins cannot access the `core` namespace._

## Files

### coronium.lua

`/usr/local/coronium/lualib/coronium.lua`

Holds the initalization object and also loads in any plugins that have been specified in the file.

## Setup

To set up the user and passwords for the databases run:

`sudo /usr/local/coronium/sbin/coronium-dbsetup`

# Public

## User Directories

### Projects

`/home/coronium/projects`

Where all the project directories live.

### Plugins

`/home/coronium/projects/plugins`

Holds user created plugins. Plugins can be accessed in projects by:

```lua
local plugin = require("plugins.<plugin-name>")
```

Note: _Plugins cannot access the `core` namespace._
