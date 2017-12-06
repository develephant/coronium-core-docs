You can use error status codes to help your application deals with errors. The __status__ code, if any, can be found in the error response event. Not all errors will return status codes. The default status of an error is -1.

__Example__

```lua
local function onResponse( evt )
  if evt.error then
    print(evt.status) -- error status code
  else
    --no error
  end
end
```

## Users

|Code|Description|
|----|-----------|
|600|User already exists.|
|601|User could not be created.|
|604|Registered User could not be found.|
|614|Pending User could not be found.|

## Email

|Code|Description|
|----|-----------|
|701|Email could not be sent.|
|702|Email configuration is not set or invalid.|

## Default

|Code|Description|
|----|-----------|
|-1|Catch all error status code.|