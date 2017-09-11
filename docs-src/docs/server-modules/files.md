Provides methods to manage server side files.

!!! info "Upload / Download"
    File transfers are performed using the client-side __[Files](/client-module/files/)__ module.

!!! tip ""
    If you'd like to transfer files using __[Amazon S3](https://aws.amazon.com/s3/)__ take a look at the __[S3 Lite](https://marketplace.coronalabs.com/plugin/s3-lite)__ plugin for Corona.

### list

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

!!! info ""
    This method will only list the _files_ in the directory, not any directories. If no files are present, it will return an empty table.

### move

Move a file in the server files directory.

```lua
core.files.move(srcpath, destpath, overwrite)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|srcpath|The source filepath with extension.|_String_|__Y__|
|destpath|The destination filepath with extension.|_String_|__Y__|
|overwrite|If __true__ overwrite existing files (default __false__).|_Boolean_|__N__|


__Example__

```lua
local ok, err = core.files.move("imgs/image01.png", "assets/image01.png")
```

### copy

Copy a file in the server files directory.

```lua
core.files.copy(srcpath, destpath, overwrite)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|srcpath|The source filepath with extension.|_String_|__Y__|
|destpath|The destination filepath with extension.|_String_|__Y__|
|overwrite|If __true__ overwrite existing files (default __false__).|_Boolean_|__N__|


__Example__

```lua
local ok, err = core.files.copy("imgs/image01.png", "assets/image01.png")
```

### remove

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

### rename

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

!!! note ""
    This rename method will not move a file. Any path supplied to the __new_name__ parameter will be ignored.
