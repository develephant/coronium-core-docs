Once a user has been registered in the system, you can log them in to your application by using the __[login](/client/modules/users/api/#login)__ method. A "login" provides you with the users basic information and state, allowing you to work with the user. 

In addition, a successful login of an active user marks a special login event to help track your application logins per user, which can be viewed in the __[Webmin user section](/server/webmin/setup/)__.

## Login Object

On a successful log in, the __result__ will hold a basic login object as a __table__, with the following keys:

|Name|Description|Type|
|----|-----------|----|
|active|Whether the user is in an active state.|_Boolean_|
|confirmation|If using email confirmation, will hold sent status.|_String_ or _Nil_|
|email|The email address associated with the user, if any.|_String_ or _Nil_|
|extra|Any extra meta data associated with the user, if any.|_Table_ or _Nil_|
|scope|The application scope for this user.|_String_|
|user_id|The users unique identifier.|_String_|
|username|The users chosen username.|_String_|
|validated|Whether the user is active and confirmed (if using email confirmation).|_Boolean_|

__Example__

```lua
local function onUserLogin( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id) --result is a login object
  end
end

core.users.login({
  username = "Donna",
  password = "1234"
}, onUserLogin)
```

## Checking State

Using the various "state" keys returned with the login object, you can determine the status of a user and make decisions in your application logic accordingly. 

### validated

The __validated__ key is a convenience state that allows you to check if the user is in both an "active" and "confirmed" state (if using email confirmation). You can use this value to quickly validate a user for access to your application.

__Example__

```lua
local function onUserLogin( evt )
  if evt.error then
    print(evt.error)
  else
    if evt.result.validated then
      --user is cleared for application usage
    end
  end
end

core.users.login({
  username = "Donna",
  password = "1234"
}, onUserLogin)
```

### active

To check if the user is in an "active" state, you can check the __active__ key. If using email confirmation, a user will not be active until the confirmation has been acknowledged.

The active state allows you to mark a user disabled or enabled. You can change the active state of a user using the __[update](api/#update)__ method.

__Example__

```lua
local function onUserLogin( evt )
  if evt.error then
    print(evt.error)
  else
    if evt.result.active then
      --user is in an active state 
    end
  end
end

core.users.login({
  username = "Donna",
  password = "1234"
}, onUserLogin)
```

!!! note
    While you can log in an "inactive" user, a login event _is not_ stored.

### confirmation

When using email confirmation, the login object will contain a __confirmation__ key with the sent status of the confirmation email. 

The key will contain a string value of either 'pending' if the email was sent successfully, or 'failed' on error. If the user has already acknowledged the confirmation email this key will be _Nil_.

__Example__

```lua
local function onUserLogin( evt )
  if evt.error then
    print(evt.error)
  else
    local confirm = evt.result.confirmation
    if confirm and confirm.confirmation == 'pending' then
      --confirmation email was sent successfully, but not ackowledged
    end
  end
end

core.users.login({
  username = "Donna",
  password = "1234"
}, onUserLogin)
```

## Logging Out

The login functionality itself does nothing special as far as setting any type of "token" or similar. Because of this, there is no special "logout" functionality. You as the developer determine when a user is logged out of your application.
