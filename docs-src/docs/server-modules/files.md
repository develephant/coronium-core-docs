# Overview

Provides methods to manage server side files.

!!! note
    Uploads and Downloads are performed using the client-side __[Files](/client-module/files/)__ module.

---

## remove

Remove a file from the server files directory. Returns __true__ on success, or __nil__ and an error.

```lua
core.files.remove(filepath)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|filepath|The source filepath.|_String_|__Y__|

__Example__

```lua
local ok, err = core.files.remove("imgs/image002.png")
```

---

## rename

Rename a file in the server files directory. Returns __true__ on success, or __nil__ and an error.

```lua
core.files.rename(filepath, new_name)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|filepath|The source filepath.|_String_|__Y__|
|new_name|The name to rename the file to.|_String_|__Y__|

__Example__

```lua
local ok, err = core.files.rename("imgs/image001.png", "image002.png")
```

!!! note
    This method will not move a file. Any path supplied to the __new_name__ parameter will be ignored.

---

## list

List the files in the specified directory path. Returns a __table__ array, or __nil__ and an error.

```lua
core.files.list(dirpath)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|dirpath|The directory to list.|_String_|__Y__|

```lua
local list, err = core.files.list("imgs")
```

!!! note
    This method will only list the _files_ in the directory, not any directories. If no files are present, it will return an empty table.

---

!!! tip "Amazon S3"
    If you'd like to transfer files using __[Amazon S3](https://aws.amazon.com/s3/)__ take a look at the __[S3 Lite](https://marketplace.coronalabs.com/plugin/s3-lite)__ plugin for Corona.