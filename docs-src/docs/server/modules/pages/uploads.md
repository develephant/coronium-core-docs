The Coronium Pages module supports file uploads using the 'multipart/form-data' form encoding type. These uploaded files can be stored in the Coronium _files_ directory for further usage.

## Accessing Uploads

Uploaded files are stored in the `page.files` property of the `page` object as _file objects_. You can access a file object by its form input name. For sake of an example, let us assume the following form to upload a file:

```html
<form action="" method="POST" enctype="multipart/form-data">
  <input type="file" name="avatar" id="avatar">
  <button type="submit">Upload Avatar</button>
</form>
```

Once this form is posted to the Coronium Page, you can access the "avatar" file object like so:

```lua
local avatarObj = page.files.avatar
```

## File Upload Objects

File objects have a handful of properties that you can inspect. This can be helpful when looking for a specific file type or size.

__File Object Properties__

|Name|Description|
|----|-----------|
|file|The raw name the uploaded file.|
|type|The file [MIME](https://www.iana.org/assignments/media-types/media-types.xhtml) type. Example: 'image/png'.|
|size|The total file size in bytes.|

For example, to verify an upload is a `.png` image:
```lua
local avatarObj = page.files.avatar

if avatarObj.type == 'image/png' then
  --save file upload
else
  --wrong file type, discard upload
end
```

## Handling Uploads

Once you have a reference to a file object, you can save it to the _files_ directory on your Coronium server. 

All uploads are stored in a temporary directory until saved. If you determine that the file is not needed, or is of the incorrect type or size, you should "discard" the uploaded file to keep this temporary directory clean (see [Discarding](/server/modules/pages/uploads/#discarding) below).

When you successfully save an upload, the uploaded file is automatically removed from the temporary directory.

### Saving

To save an uploaded file, use the `page.saveFile` method with a reference to the file object:

```lua
local avatarObj = page.files.avatar

local info, err = page.saveFile(avatarObj, '/avatars')

if not info then
  core.log(err) -- log error
else
  core.log("Saved file at "..info.path)
end
```

The `saveFile` method returns an `info` object on success. See the __[saveFile](/server/modules/pages/api/#savefile)__ method for details on what keys this object exposes.

__The `unique` Flag__

By default, an uploaded file will be saved using its "raw" name as submitted by the form. To create a unique name for the file upload -- to prevent overwriting an existing file for example -- you can pass the `unique` boolean flag to the `saveFile` method.

```lua
local avatarObj = page.files.avatar

--pass true as the third argument to create a unique name
local info, err = page.saveFile(avatarObj, '/avatars', true)

if not info then
  core.log(err) -- log error
else
  core.log("Saved file as "..info.file) --info.file holds the unique name
end
```

__Storage Paths__

Files are stored relative to the _files_ directory on your Coronium server. In the two examples above, we have provided '/avatars' as the relative path to store the image. This would result in the image being stored at ___/home/coronium/files/avatars/<image-name\>.png___.

### Discarding

As mentioned above, if for any reason you decide to _not_ save the uploaded file, you should "discard" it to keep the temporary directory clean.

You can discard a file by using the __[discardFile](/server/modules/pages/api/#discardfile)__ method with a reference to the file object you want to discard.

Expanding on the first example above:

```lua
local avatarObj = page.files.avatar

if avatarObj.type == 'image/png' then
  --save file upload
  local info, err = page.saveFile(avatarObj, '/avatars')
  ...
else
  --wrong file type, discard upload
  local success, err = page.discardFile(avatarObj)
  ...
end
```

!!! note "JavaScript"
    You can also check a file type or size on the browser client using JavaScript before uploading.

## Multiple Uploads

Handling multiple uploads is as simple as accessing the file objects and either saving or discarding them.

An example multiple upload form might look like:

```html
<form action="" method="POST" enctype="multipart/form-data">
  <input type="file" name="avatar" id="avatar">
  <input type="file" name="logo" id="logo">
  <button type="submit">Upload</button>
</form>
```

The file objects can be accessed and saved like so:

```lua
local avatarObj = page.files.avatar
local logoObj = page.files.logo

-- Save avatar
local info, err = page.saveFile(avatarObj, '/avatars')
if not info then
  core.log(avatarObj.file..' had error: '..err)
end

-- Save logo
local info, err = page.saveFile(logoObj, '/logos')
if not info then
  core.log(logoObj.file..' had error: '..err)
end
```

## Page Upload Example

A very basic file upload page.

```lua
--# upload.lua
local page = core.pages.new()

--# Generate form (GET)
local function renderForm()
  local formBody = [[
  <form action="" method="POST" enctype="multipart/form-data">
    <input type="file" name="avatar" id="avatar">
    <button type="submit">Upload</button>
  </form>    
  ]]

  page.response(formBody)
end

--# Process form (POST)
local function submitForm()
  local avatarObj = page.files.avatar

  local info, err = page.saveFile(avatarObj, '/avatars', true)
  if not info then
    page.response("File upload failed: "..err)
  else
    page.response("Uploaded file: "..info.file)
  end
end

--# Incoming Page Request
if page.isGet then
  renderForm()
elseif page.isPost then
  submitForm()
else
  page.status(501)
end
```