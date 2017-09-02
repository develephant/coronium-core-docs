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
|baseDir|A Corona directory constant.|_[Const]()_|__Y__|
|destFilePath|Remote destination path with extension.|_String_|__Y__|
|listener|The upload listener callback function.|_Function_|__Y__|

__Upload Listener__

```lua
local function uploadListener( evt )
  if evt.error then
    print( evt.error )
  else
    if not evt.result.done then
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
|transform|Optional parameters for image transform (See below).|_Table_|__N__|

__Download Listener__

```lua
local function downloadListener( evt )
  if evt.error then
    print( evt.error )
  else
    if not evt.result.done then
      print( evt.progress )
    else
      print("file download complete")
    end
  end
end
```

__Example__


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
```