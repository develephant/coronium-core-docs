Provides methods to register, login, and track users for your applications.

!!! tip "Screencasts Available"
    Get an overview of using the User module in a screencast format by clicking one of the following links: __[Creating Users](/screencasts/#creating-users)__, __[Updating Users](/screencasts/#updating-users)__, __[Confirming Users](/screencasts/#confirming-users)__.

### login

Retrieve the users meta data, and marks a _login event_ in the __users__ database.

```lua
core.users.login(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|username|The registered username.|_String_|__Y__|
|password|The users password.|_String_|__Y__|

__Event Response__

On success, the __result__ will hold the user object as a __table__.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id) -- result holds the user object
  end
end

core.users.login({username="tiny", password="abcd1234"}, apiListener)
```

!!! info ""
    See also the __[Logging in](/users-guide/login/)__ section of the __Users Guide__.

### create

Create a new user, and optionally request an email confirmation.

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
|extra|A custom data table of key/value pairs to store with the user. Can be of type __String__ or __Number__ only.|_Table_|__N__|
|confirmation|Parameters for _optional_ email confirmation. See the __[Confirmation](/users-guide/confirmation/)__ section in the __Users Guide__.|_Table_|__N__|

!!! warning "Extra Metadata"
    If you use the `extra` metadata table, the values can be of type __String__ or __Number__ only. The `extra` metadata table cannot contain other tables. 

!!! info
    Plain text passwords are hashed before being sent to the server. Do not try to hash the password yourself.

__Event Response__

On success, the __result__ will hold the created users unique identifier as a __string__.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result) -- result holds the users id
  end
end

core.users.create({
  username="tiny", 
  password="abcd1234"
}, apiListener)
```

!!! tip "Email Confirmation"
    See the __[Confirmation](/users-guide/confirmation/)__ section in the __Users Guide__ for examples on using email confirmation.

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
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id) -- result holds the user object
  end
end

core.users.get({user_id="289bc36e-0df7-44df-99b1-a6543c6f45eb"}, apiListener)
```

### update

Update a users data in the database.

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
|update|The user data to update as key/value pairs.|_Table_|__Y__|

!!! warning "Extra Metadata"
    If you use the `extra` metadata table, the values can be of type __String__ or __Number__ only. The `extra` metadata table cannot contain other tables. To remove an item from the metadata table see the __[core.users.NULL](#null)__ constant.

__Event Response__

On success, the __result__ will hold the updated user object as a __table__.

__Example__

```lua
local function apiListener( evt )
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
    extra = {
      color = "Blue"
    }
  }
}, apiListener)
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

__Event Response__

On success, the __result__ will hold the records updated as a __number__. This will most always be __1__.

__Example__

```lua
local function apiListener( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result) -- 1
  end
end

core.users.delete({user_id="289bc36e-0df7-44df-99b1-a6543c6f45eb"}, apiListener)
```

## Constants

### NULL

Used to remove a key from the users `extra` metadata (see __[create](#create)__). Can only be used with the __[update](#update)__ method.

```lua
core.users.NULL
```

__Example__

```lua
local function apiListener( evt )
  ...
end

core.users.update({
  user_id = "<user_id>",
  update = {
    extra = {
      color = core.users.NULL --remove the `color` key
    }
  }
}, apiListener)
```

## Viewing Users

You can perform some simple administration and view user statistics in the __[Webmin](/webmin-guide/setup/)__.

For more complex user management, [log into your MySQL server](/server-modules/mysql/#administration) and browse the __coronium__ database.