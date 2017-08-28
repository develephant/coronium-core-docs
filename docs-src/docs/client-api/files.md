# Overview

Upload and download files to and from the server.

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