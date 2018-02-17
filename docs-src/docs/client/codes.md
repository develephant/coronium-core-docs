Error __status__ codes can help you shape your application logic. The status code, if any, can be found in the response event when an error occurs, in the event `status` key.

Not all module methods return status codes. The default status of an error is -1.

__Example__

```lua
-- client-side response listener
local function onResponse( evt )
  if evt.error then
    print(evt.status) -- error status code
  else
    --no error
  end
end
```

## Modules

### Users

|Code|Description|
|----|-----------|
|600|User already exists.|
|601|User could not be created.|
|604|Registered User could not be found.|
|605|User requires server-side login.|
|614|Pending User could not be found.|
|620|OAuth provider already exists for user.|
|624|OAuth provider does not exist for user.|

### Email

|Code|Description|
|----|-----------|
|701|Email could not be sent.|
|702|Email configuration is not set or invalid.|

### General

|Code|Description|
|----|-----------|
|800|Result was invalid.|
|801|Mismatched parameter type.|
|804|Required parameter is missing.|
|810|General API error.|
|815|General Network error.|
|820|General IO error.|
|830|JSON decode failed.|
|831|JSON encode failed.

### MySQL

|Code|Description|
|----|-----------|
|901|Unauthorized database.|

### Scopes

|Code|Description|
|----|-----------|
|1000|Scope name already exists.|
|1004|Scope was not found.|

## Custom Status Codes

If you are using a your own [server-side API](/server/modules/api/), you can pass a custom status code using the __[core.error](/server/modules/api/#errors)__ method.