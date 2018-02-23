Provides methods to register, login, and track users for your applications. 

For more detailed information on working with users, see the __[Creating](creating/)__, __[Updating](updating/)__, __[Logging In](login/)__, and __[Confirmation](confirmation/)__ sections.

See also the __[OAuth API](/client/modules/users/oauth/)__ for managing users with OAuth capabilities.

### create

Create a new user, and optionally request an email confirmation.

- See also: __[Creating](creating/)__ users, and __[Confirmation](/client/modules/users/confirmation/)__ using email.

```lua
core.users.create(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|username|The preferred username.|_String_|__Y__|
|password|The users password.|_String_|__Y__|
|email|The users email. _User email is required if using email confirmation._ See `confirmation` parameter below.|_String_|__N__|
|group|A custom string used to partition this user within the scope.|_String_|__N__|
|extra|A custom data table of key/value pairs to store with the user. Can be of type _String_, _Number_, or _Boolean_ only.|_Table_|__N__|
|confirmation|Parameters for _optional_ email confirmation. See the __[Confirmation](/client/modules/users/confirmation/)__ section for detailed usage.|_Table_|__N__| 

!!! warning "Important"
    Passwords are hashed before being sent to the server. Do not try to hash the password yourself.

!!! info "OAuth Users"
    To add OAuth provider information to a user, see the __[addAuthProvider](/client/modules/users/oauth/#addauthprovider)__ method.

__Event Response__

On success, the __result__ will hold the created users unique identifier in the __user_id__ key.

__Example__

```lua
local function onUserCreate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id) -- holds the users id
  end
end

core.users.create({
  username="tiny", 
  password="abcd1234"
}, onUserCreate)
```

### login

Retrieves the users basic data, and marks a _login event_ in the __users__ database. 

You can log a user in with either a __username__ _or_ __email__ (if the user has provided one).

- See also: __[Logging In](login/)__ users, and __[OAuth Login](/client/modules/users/login/#oauth-login)__.

```lua
core.users.login(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params (Username Login)__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|username|The registered username.|_String_|__Y__|
|password|The users password.|_String_|__Y__|

__Data Params (Email Login)__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|email|The registered email.|_String_|__Y__|
|password|The users password.|_String_|__Y__|

!!! info "OAuth Login Parameters"
    To log in a user with an OAuth provider, see __[OAuth Login](/client/modules/users/login/#oauth-login)__.

__Event Response__

On success, the __result__ will hold a basic login object as a __table__. See the __[Logging In](login/)__ section for more details.

__Example__

_Username login_

```lua
local function onUserLogin( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id) -- result holds the login object
  end
end

core.users.login({username="tiny", password="abcd1234"}, onUserLogin)
```

_Email login_

```lua
local function onUserLogin( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id) -- result holds the login object
  end
end

core.users.login({email="tina@home.com", password="abcd1234"}, onUserLogin)
```

### get

Get a users full data record. This method __does not__ mark a _login event_ for the user. See [login](#login).

```lua
core.users.get(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|

__Event Response__

On success, the __result__ will hold the user object as a __table__.

__Example__

```lua
local function onUserGet( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id) -- result holds the user object
  end
end

core.users.get({user_id="289bc36e-0df7-44df-99b1-a6543c6f45eb"}, onUserGet)
```

### update

Update a users data in the database.

- See also: __[Updating](updating/)__ users.

```lua
core.users.update(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|update|The user data to update as key/value pairs. See the __[Updating](/client/modules/users/updating/)__ section for detailed usage.|_Table_|__Y__|

!!! info "OAuth Users"
    To update a users OAuth provider information, see the __[updateAuthProvider](/client/modules/users/oauth/#updateauthprovider)__ method.

__Event Response__

On success, the __result__ will hold the updated user object as a __table__.

__Example__

```lua
local function onUserUpdate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id) -- result is the updated user object
  end
end

core.users.update({
  user_id = "289bc36e-0df7-44df-99b1-a6543c6f45eb",
  update = {
    email = "some@email.com",
    password = "abcd"
  }
}, onUserUpdate)
```

### delete

Delete a users record from the __users__ database.

```lua
core.users.delete(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|

!!! info "OAuth Users"
    To delete a users OAuth provider information, see the __[removeAuthProvider](/client/modules/users/oauth/#removeauthprovider)__ method.

__Event Response__

On success, the __result__ will hold the records removed as a __number__. Generally a __1__ or __0__.

__Example__

```lua
local function onUserDelete( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result) -- 1 or 0
  end
end

core.users.delete({user_id="289bc36e-0df7-44df-99b1-a6543c6f45eb"}, onUserDelete)
```

### resendConfirmation

Resend the user confirmation email. See __[Resending Confirmation](confirmation/#resending-confirmation)__.

```lua
core.users.resendConfirmation(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|

__Event Response__

On success, the __result__ will hold the users unique identifier as a __string__.

__Example__

```lua
local function onResend( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result) -- result holds the users id
  end
end

core.users.resendConfirmation({
  user_id="289bc36e-0df7-44df-99b1-a6543c6f45eb"
}, onResend)
```


## Constants

### NULL

Used to remove a key from the users `extra` metadata (see __[create](#create)__). Can only be used with the __[update](#update)__ method.

```lua
core.users.NULL
```

__Example__

```lua
local function onUserUpdate( evt )
  ...
end

core.users.update({
  user_id = "<user_id>",
  update = {
    extra = {
      color = core.users.NULL --remove the `color` key
    }
  }
}, onUserUpdate)
```

## Viewing Users

You can perform some simple administration and view user statistics in the __[Webmin](/server/webmin/setup/)__.
