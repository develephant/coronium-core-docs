What follows are a number of different user creation styles and example code.

## Basic

To create a user without email confirmation, a minimum of a __username__ and __password__ is required.

__Example__

```lua
local function onUserCreate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id)
  end
end

core.users.create({
  username = "Donna",
  password = "1234"
}, onUserCreate)
```

You can optionally add an __email__ address:

__Example__

```lua
local function onUserCreate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id)
  end
end

core.users.create({
  username = "Donna",
  password = "1234",
  email = "me@home.com"
}, onUserCreate)
```

## Basic w/ Extra

When creating a user, you can optionally add an __extra__ table of metadata to associate with the user. The __extra__ table can only contain _String_, _Number_, and _Boolean_ data types.

__Example__

```lua
local function onUserCreate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id)
  end
end

core.users.create({
  username = "Donna",
  password = "1234"
  extra = {
    color = "Blue",
    age = 24,
    winner = true
  }
}, onUserCreate)
```

## Email Confirmation

To send an email confirmation the __email__ and __confirmation__ keys are required. See the __[Confirmation](confirmation/)__ section for more information.

__Example__

```lua
local function onUserCreate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id)
  end
end

core.users.create({
  username = "Donna",
  password = "1234",
  email = "me@home.com",
  confirmation = {
    from_email = "app@domain.com",
    subject = "Thanks for signing up!"
  }
}, onUserCreate)
```

### Response Event

When creating a user with email confirmation, the response __result__ will be a table containing the newly registered users __user_id__, and a __confirmation__ key.

The __confirmation__ key will contain the email sent state as a string with a value of 'pending' on success, or 'failed' otherwise.

## With OAuth Provider

You can add OAuth provider information (using the Facebook login for example) after the user has been created, by using the __[addAuthProvider](/client/modules/users/oauth/#addauthprovider)__ method.

__Example__

```lua
-- First gather the client_id, access_token, etc. from the Facebook login.

--A method to call after user has been created
local function addOAuthInfo(user_id)

  --== Link auth provider to the user
  local function onAuthAdded( evt )
    if evt.error then
      print(evt.error)
    else
      print("Auth provider has been added!")
    end
  end

  core.users.addAuthProvider({
    provider = core.FACEBOOK,
    client_id = fb.client_id,
    access_token = fb.access_token,
    access_token_expiry = fb.access_token_expiration
  }, onAuthAdded)
end

--== Create the user
local function onUserCreated( evt )
  if evt.error then
    print(evt.error)
  else
    --we have a successful user creation
    addOAuthInfo(evt.result.user_id)
  end
end

core.users.create({
  username = 'Timmy',
  password = '1234',
  email = 'my@email.com',
  group = 'cadets',
  extra = {
    color = 'blue'
  }
}, onUserCreated)
```

You can now log in the user normally, with `username` and `password` or using the OAuth information (see __[OAuth Login](/client/modules/users/login/#oauth-login)__).

## Devices As Users

If you don't plan on having traditional users for your app, there is still an interesting use case for using the actual device as the user. This allows one to capture additional statistics that can be viewed in the __[Webmin](/server/webmin/setup/)__.

While the __[appOpened](/client/modules/analytics/#appopened)__ analytics method tracks each time an application is opened on a device, it doesn't capture how many unique devices are actually running the app. 

By co-opting the user system, we can track how many unique devices have "joined" your app, as well as, how many times a specific device has "logged" in to your app.

This also opens up the ability to store meta data specific to a device "user", or provide time limited "tokens" that can trigger special events.

### Register a Device

To use a device as a user, we can use the __[OpenUDID Plugin](https://docs.coronalabs.com/plugin/openudid/index.html)__ provided freely by __Corona__ for iOS. 

For Android we can use the Corona method `system.getInfo("deviceID")` (see below for how to get a consistent [Android UDID](#android-udid)). We then use the device ID as our OAuth client ID. 

We'll also need to create a server-side API method to login/register the device from the client-side. See the server-side __[API](/server/modules/api/)__ documentation for more information on creating server-side APIs.

__Server-Side API Module__

The following module and method will attempt to login the "device" or register it into the user system.

```lua
local api = core.api()

function api.checkInDevice(input, scope)

  local udid = input.udid
  local user_id = nil

  --first check if a device ID exists by trying to log in
  local res, err = core.users.login({
    client_id = udid,
    provider = core.OPENUDID,
    scope = scope
  })

  if not res then
    --a "user" with this udid does not
    --exist, so create an anonymous one
    user_id, err = core.users.register(nil, nil, scope, {
      meta = {
        device_id = udid,
        group = 'device',
        login = true --mark this as a login event also
      }
    })
    --check for error
    if not user_id then
      return core.error(err)
    end

    --link the device "user" to an OPENUDID auth provider
    local oauth, err = core.users.addAuthProvider(user_id, core.OPENUDID, {
      client_id = udid
    })
    --check for error
    if not oauth then
      return core.error(err)
    end

    --device "user" has been registered, pass a creation flag
    return "created"

  else
    --OR we successfully logged in, pass back the device "user" data
    return res
  end

return api
```

### Client Side Usage

Now each time our application opens we call the `checkInDevice` server-side method at which point we will start to gather statistics about each device "join" and "login" events that can be viewed in the __[Webmin](/server/webmin/setup/)__.

```lua
-- require the OpenUDID plugin
local openudid = require("plugin.openudid")
local core = require("plugin.coronium-core")

--========================================
--== Coronium intialization goes here
--========================================

local udid
local platform = system.getInfo("platform")

if platform == 'ios' then
  udid = openudid.getValue()
elseif platform == 'android' then
  udid = system.getInfo("deviceID") --== See Android UDID notes below
end

--== Set up the response listener
local function onDeviceCheckIn( evt )
  if evt.error then
    print(evt.error)
  else
    print("Device checked in")
    --optionally get the device record
    if evt.result ~= "created" then
      local device_record = evt.result
    end
  end
end

--== Check-in the device
core.api.checkInDevice({
  udid = udid
}, onDeviceCheckIn)

```

See also __[Custom API Init](/client/guide/#custom-api-init)__.

### Android UDID

To make sure you get a consistent Android UDID see the __[system.getInfo("deviceID")](https://docs.coronalabs.com/api/library/system/getInfo.html#deviceid)__ Corona documentation on the subject.











