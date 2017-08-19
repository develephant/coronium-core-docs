## init

Initialize the SkyTable module.

```lua
:init(config)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|config|The SkyTable configuration.|Table|__Y__|

__Config Properties__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user|The SkyTable user.|String|__Y__|
|password|The user password.|String|__Y__|
|base|The SkyTable base.|String|__Y__|
|host|The SkyTable server host.|String|__Y__|
|key|The SkyTable server key|String|__Y__|
|debug|Output response data.|Boolean|N|

__Example__

```lua
skytable:init({
  user = "<user-email>",
  password = "<user-password>",
  base = "app1",
  host = "http://<skytable-host>:7173",
  key = "<server-key>",
  debug = true
})
```

!!! important
    The SkyTable server runs on port __7173__, make sure to include this on the _host_ property; `http://<skytable-host>:7173`.

---

## open

Open a SkyTable for usage.

```lua
:open(table_name)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|table_name|The name of the SkyTable to use.|String|__Y__|

__Example__

```lua
local profile = skytable:open("profile")
```

---

## set

Set a value in a SkyTable.

```
:set([data_path,] data, listener[, options])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_path|Path to the item data. (See [Data Paths](guide/#data-paths))|String|N|
|data|The data to set the item to.|String|__Y__|
|listener|A listener function. (See [Listeners](guide/#listeners))|Function|__Y__|
|options|Additional options.|Table|N|

__Options Properties__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tag|A descriptive name for the event. (See [Tags](guide/#tags))|String|N|
|flag|__NX__ or __XX__. (See [Flags](guide/#flags))|String|N|
|expiry|Table expiration in seconds. (See [Expiry](guide/#expiry))|Number|N|

__Example__

```lua
profile:set("age", 23, onSetResult)
```

!!! tip
    See __[Listeners](guide/#listeners)__ and __[Data Paths](guide/#data-paths)__ in the _Client Guide_ for detailed usage information.

---

## get

Get a value from a SkyTable.

```
:get([data_path,] listener[, options])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_path|Path to the item data. (See [Data Paths](guide/#data-paths))|String|N|
|listener|A listener function. (See [Listeners](guide/#listeners))|Function|__Y__|
|options|Additional options.|Table|N|

__Options Properties__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tag|A descriptive name for the event. (See [Tags](guide/#tags))|String|N|

__Example__

```lua
profile:get("age", onResult)
```

!!! tip
    See __[Listeners](guide/#listeners)__ and __[Data Paths](guide/#data-paths)__ in the _Client Guide_ for detailed usage information.

---

## delete

Delete a value in a SkyTable.

```
:delete([data_path,] listener[, options])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_path|Path to the item data. (See [Data Paths](guide/#data-paths))|String|N|
|listener|A listener function. (See [Listeners](guide/#listeners))|Function|__Y__|
|options|Additional options.|Table|N|

__Options Properties__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tag|A descriptive name for the event. (See [Tags](guide/#tags))|String|N|

__Example__

```lua
profile:delete("color", onResult)
```

!!! tip
    See __[Delete](guide/#delete)__ in the _Client Guide_ for detailed usage information.

---

## keys

Returns the SkyTable keys.

```
:keys([data_path,] listener[, options])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_path|Path to the item data. (See [Data Paths](guide/#data-paths))|String|N|
|listener|A listener function. (See [Listeners](guide/#listeners))|Function|__Y__|
|options|Additional options.|Table|N|

__Options Properties__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|tag|A descriptive name for the event. (See [Tags](guide/#tags))|String|N|

__Example__

```lua
profile:keys(onResult)
```