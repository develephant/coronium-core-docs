# Overview

Upload and download files to and from the server.

---

## upload

Upload a file to the server.

```lua
core.files.upload( srcFile, srcDirectory, destFilePath, listener )
```

__Upload Listener:__

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

---

## download

Download files from the server.

```lua
core.files.download( srcFilePath, destFile, destDir, listener )
```

__Download Listener:__

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

### Image Transforms

Download an image with transformations.

```lua
core.files.download( srcFilePath, destFile, destDir, listener, transform )
```

!!! note
    Transforms only work with __PNG__, __JPG__, and __GIF__ file types.

__Transform Options:__

```lua
local transform = {
  width = 100,
  height = 100,
  sharpen = 50
}
```