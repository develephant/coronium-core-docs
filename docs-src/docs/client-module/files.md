# Overview

This module allows you to transfer files to and from your __Coronium Core__ server.

!!! tip "Amazon S3"
    If you'd like to transfer files using __[Amazon S3](https://aws.amazon.com/s3/)__ take a look at the __[S3 Lite](https://marketplace.coronalabs.com/plugin/s3-lite)__ plugin for Corona.

---

## upload

Upload a file to the server.

```lua
core.files.upload(srcFilePath, baseDir, destFilePath, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|srcFilePath|Local source file path with extension.|_String_|__Y__|
|baseDir|A Corona directory constant.|_[Const](https://docs.coronalabs.com/api/library/system/index.html#constants)_|__Y__|
|destFilePath|Remote destination path with extension.|_String_|__Y__|
|listener|The upload listener callback function.|_Function_|__Y__|
|options|See __Upload Options__ below.|_Table_|__N__|

__Upload Options__

The upload options table supports the following keys:

|Name|Description|Type|Default|
|----|-----------|----|-------|
|safe|Do not overwrite existing files.|_Boolean_|__false__|
|unique|Generate a unique file name for the file.|_Boolean_|__false__|

__Event Response__

While uploading, a __progress__ key will be available.

When the upload is complete, the __result__ will contain the following keys:

|Name|Description|Type|
|----|-----------|----|
|path|The server-side directory path to the file.|_String_|
|file|The server-side file name with extension.|_String_|


__Upload Listener__

```lua
local function uploadListener( evt )
  if evt.error then
    print( evt.error )
  else
    if evt.progress then
      print( evt.progress )
    else
      print("file upload complete")
      print("filePath: ", evt.result.path)
      print("fileName: ", evt.result.file)
    end
  end
end
```

__Example__

```lua
core.files.upload( 
  "image.png", 
  system.DocumentsDirectory, 
  "imgs/image001.png", 
  uploadListener)
```

__Upload Progress__

By default the listener event returns a __progress__ key with the current upload progress as a decimal value between 0 and 1 that you can use to create progress bars, etc.

If you don't care about the progress, you can write the listener function like so:

```lua
local function uploadListener( evt )
  if evt.error then
    print(evt.error)
  else
    if not evt.progress then
      print("file upload complete")
    end
  end
end
```

---

## download

Download files from the server.

```lua
core.files.download(srcFilePath, destFilePath, baseDir, listener[, transform])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|srcFilePath|Remote source file path with extension.|_String_|__Y__|
|destFilePath|Local destination path with extension.|_String_|__Y__|
|baseDir|A Corona system directory constant.|_[Const](https://docs.coronalabs.com/api/library/system/index.html#constants)_|__Y__|
|listener|The download listener callback function.|_Function_|__Y__|
|transform|See __Image Transforms__ below.|_Table_|__N__|

__Event Response__

While downloading, a __progress__ key will be available.

When the download is complete, the file will be available in the directory set in the __baseDir__ parameter.

__Download Listener__

```lua
local function downloadListener( evt )
  if evt.error then
    print( evt.error )
  else
    if evt.progress then
      print( evt.progress )
    else
      print("file download complete")
    end
  end
end
```

__Example__

```lua
core.files.download(
  "imgs/image001.png",
  "image.png",
  system.DocumentsDirectory,
  downloadListener)
```


__Download Progress__

By default the listener event returns a __progress__ key with the current download progress as a decimal value between 0 and 1 that you can use to create progress bars, etc.

If you don't care about the progress, you can write the listener function like so:

```lua
local function downloadListener( evt )
  if evt.error then
    print(evt.error)
  else
    if not evt.progress then
      print("file download complete")
    end
  end
end
```

### Image Transforms

Download an image with transformations.

!!! note
    Transforms only work with __PNG__, __JPG__, and __GIF__ file types.

__Transform Keys__

|Name|Description|Type|Default|
|----|-----------|----|-------|
|width|Proportionally resize image based on width.|_Number_|nil|
|height|Proportionally resize image based on height.|_Number_|nil|
|sharpen|Increases sharpness of the final image (0-100).|_Number_|0|

__Example__

```lua
local transform = {
  width = 100,
  height = 100,
  sharpen = 50
}

core.files.download(
  "imgs/image001.png",
  "image.png",
  system.DocumentsDirectory,
  downloadListener,
  transform)
```

---

## remove

Delete a file from the server. ___Once deleted the file is not recoverable.___

```lua
core.files.remove(srcFilePath, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|srcFilePath|Remote source file path with extension.|_String_|__Y__|
|listener|The listener callback function.|_Function_|__Y__|

__Event Response__

On success, the __result__ key will be __true__.

__Example__

```lua
local function deleteListener( evt )
  if evt.error then
    print(evt.error)
  else
    print("Deleted")
  end
end

core.files.remove("imgs/image001.png", deleteListener)
```

!!! tip
    You can also remove files by connecting to the server via SFTP. But, if you are keeping a record of the file in a database, you should remove it programatically, along with the database reference.
