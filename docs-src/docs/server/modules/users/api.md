Provides methods to build a custom user registration flow on the server-side that integrates with the Coronium Core user system, as well as, advanced user queries. You can also use OAuth client IDs as user identifiers via the __[OAuth](/server/modules/users/oauth/)__ API.

!!! warning "Advanced Usage"
    These methods are for advanced use cases. In general you should use the client-side __[Users](/client/modules/users/api/)__ module for your user management.

### register

Registers a new user into the Coronium user system. This method marks a "join" event for statistical purposes.

The user is automatically marked as confirmed and active. Email confirmations are not supported through this method. See the client-side __[Users](/client/modules/users/api/)__ module instead if you need email confirmations.

___Users created using the server-side register method are not able to log in using the client-side login method. You must create a custom server-side API method and log the user in using the server-side login method.___ See the __[login](#login)__ method below.

```lua
core.users.register(username, password, scope, meta)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|username|An optional display name for the user. To create an anonymous user pass `nil` to this parameter, and a generic name will automatically be created.|_String_ or _Nil_|__Y__|
|password|An optional password for the user. To create a user without a password, pass `nil` to this parameter. Anonymous users cannot have passwords, so if you pass `nil` for the username, you must pass `nil` here as well.|_String_ or _Nil_|__Y__|
|scope|The application scope this user should belong to. To get the current client application scope see the __[Input](/server/modules/api/#input)__ section of the API module, or supply your own.|_String_|__Y__|
|meta|Addtional information for the user registration (see Meta Keys below).|_Table_|__N__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

!!! info "Passwords"
    Supply the password as plain text. It will be hashed before being added to the user record.

__Meta Keys__

The following keys are currently supported in the `meta` table:

  - `email` (_string_)
  - `group` (_string_)
  - `extra` (_table_)
  - `login` (_boolean_)

The `login` key, when set to __true__, will mark a "login" in addition to a "join" event when the user is created. The default is __false__. This can be helpful when conditionally creating new users. See also __[login](#login)__.

The `extra` key is a key/value based table that you can use to store additional custom data to the user record. The `extra` table can only contain _String_, _Number_, and _Boolean_ data types.

The full `meta` table structure looks like:

```lua
--meta table
{
  email = "me@home.com",
  group = "cadets",
  extra = {
    color = "Blue",
    age = 24,
    winner = true
  },
  login = true
}
```

!!! warning "Anonymous Users"
    If you create an anonymous user (see examples below), you __must__ add an OAuth provider using __[addAuthProvider](/server/modules/users/oauth/#addauthprovider)__ to be able to log the user in.

__Returns__

The newly created users unique ID as a __string__, or __nil__, error, and error code.

__Example__

```lua
--Custom server-side API (main.lua)
local api = core.api()

--api methods have an additonal argument
--called `scope` that contains the 
--scope for the current client call.
function api.registerUser(input, scope)

  -- Example 1
  -- Create an anonymous user. You must add an OAuth provider
  -- to be able to log this user in.
  local user_id, err, code = core.users.register(nil, nil, scope)
  
  --== Add an OAuth provider here with the returned user_id.

  -- Example 2
  -- Create an anonymous user, with email and group. You must add an OAuth provider
  -- to be able to log this user in.
  local user_id, err, code = core.users.register(nil, nil, scope, {
    group = "cadets",
    email = "me@home.com"
  })

  --== Add an OAuth provider here with the returned user_id.

  -- Example 3
  -- Create a named user, no password.
  local user_id, err, code = core.users.register("Donna", nil, scope)

  -- Example 4
  -- Create a named user with password.
  local user_id, err, code = core.users.register("Donna", "mypass123", scope)

  -- Example 5
  -- Create a named user, with email and extra.
  local user_id, err, code = core.users.register("Billy", nil, scope, {
    email = "me@home.com",
    extra = {
      color = "Green",
      age = 36
    }
  })

  -- Example 6
  -- Create an named user in a different scope, with extra.
  local user_id, err, code = core.users.register("Bob", nil, "Space Race", {
    extra = {
      likes_pizza = true
    }
  })

  -- Handle any errors by logging
  if not user_id then
    core.log(err)
  end

  --OR

  --Pass the err and code downstream
  if not user_id then
    return core.error(err, code)
  end

  --Return something on success
  return true

end

return api
```

__Errors Codes__

The Users module methods return a string error as well as a error "status" code. You can use these codes to adjust your application logic. A listing of current error codes can be found in the __[Status Codes](/client/codes/)__ section. The codes are generally more helpful on the client-side.

__Linking OAuth Providers__

See the _[OAuth API](/server/modules/users/oauth/)_ to learn how to associate your Coronium users OAuth providers.

### login

Login a user with a __username__ (with or without password), __user_id__, or OAuth __client_id__. This method marks a "login" event for statistical purposes.

```lua
core.users.login(login_type)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|login_type|A key/value table. See example below.|_Table_|__Y__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Returns__

A user record as a __table__, or __nil__, error and error code.

__Example__

```lua
-- By username and password
local user, err, code = core.users.login({
  username='Timmy', 
  password = 'pass1234',
  scope='Fun Run'
})

-- By username
local user, err, code = core.users.login({
  username='Timmy', 
  scope='Fun Run'
})

--By OAuth ID
local user, err, code = core.users.login({
  provider = core.FACEBOOK,
  client_id="fb_id-1234", 
  scope='Space Race'
})

--By User ID
local user, err, code = core.users.login({
  user_id="aad3eba3-9c9c-9a1b-f236de1e3752"
})
```

### update

Update a user in the Coronium user system. If you do not have a `user_id`, first use the __[users.getWithQuery](#getwithquery)__ method to get one.

```lua
core.users.update(user_id, update_params)
```

!!! info "Updating OAuth"
    To update OAuth provider information for a user see the __[updateAuthProvider](#updateauthprovider)__ method.

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|update_params|A table of key/value pairs to update.|_Table_|__Y__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

The following keys are updatable:

  - `username` (_string_)
  - `password` (_string_)
  - `email` (_string_)
  - `group` (_string_)
  - `extra` (_table_)
  - `active` (_boolean_)

!!! info "Updating Passwords"
    Supply the password as plain text. It will be hashed before being updated and added to the user record.

The `extra` table can only contain _String_, _Number_, and _Boolean_ data types. You can update, add, and remove multiple keys in the `extra` table within a single update call.

```lua
--extra table
extra = {
  color = "Blue",
  age = 24,
  winner = true
}
```

To remove keys from the `extra` table you must pass a special constant `core.NULL` to the key.

```lua
--extra table
extra = {
  color = core.NULL, --remove the color key on update
  age = 24,
  winner = true
}
```

__Returns__

The updated users information as a __table__, or __nil__, error, and error code.

__Example__

```lua
--[[
Assuming the record already has an `extra` table like so:
{
  color = "Blue",
  age = 24,
  winner = true
}
--]]

--Update the user record
local res, err, errcode = core.users.update("aad3eba3...", {
  password = "superpass123",
  email = "me@town.com"
  extra = {
    color = "Red", --change color key
    high_score = 12300 --add high_score key
    age = core.NULL --remove age key
  }
})

--[[
The `extra` table for the record will now be:
{
  color = "Red",
  high_score = 12300,
  winner = true
}
--]]
```

### delete

Remove a user from the Coronium user system. If you do not have a `user_id`, first use the __[users.getWithQuery](#getwithquery)__ method to get one.

```lua
core.users.delete(user_id)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|

__Returns__

The records removed as a __number__ (usually a 1), or __nil__, error, and error code.

__Example__

```lua
local res, err, code = core.users.delete("aad3eba3-9c9c-9a1b-f236de1e3752")
```

### get

Get a users full data record. This method __does not__ mark a "login" event for the user. See [login](#login).

```lua
core.users.get(user_id)
```

!!! into "OAuth Users"
    To get a user with an OAuth provider client ID, see the __[getWithProvider](/server/modules/users/oauth/#getwithprovider)__ method in the OAuth API.

__Example__

```lua
local user, err, code = core.users.get("aad3eba3-9c9c-9a1b-f236de1e3752")
```

### getGroup

Get a set of active user records based on group and application scope.

```lua
core.users.getGroup(scope, group, limit)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|scope|The application scope to be queried.|_String_|__Y__|
|group|The group name to search for.|_String_|__Y__|
|limit|Limit the records returned. See __Limit__ below. Default is 100.|_Number_ or _Table_|__N__|

!!! info "Active Users Only"
    This method only returns users that are marked as being `active`. See __[getWithQuery](#getwithquery)__ if you need users in a group regardless of `active` status.

__Limit__

To limit the rows returned, supply a number value to the `limit` parameter. To offset the limit, supply a table array of number values. For example, to return rows 6-15, then pass {5, 10}.

__Returns__

The records found as a table array, or __nil__, error, and error code. If `limit` is set to 1, then a single table with the user data is returned (not an array).

__Example__

```lua
local api = core.api()

function api.getUserGroup(input, scope)

  --use current scope from the client calling
  local users, err, code = core.users.getGroup(scope, "cadets", 10)

  --OR

  --provide your own scope as a string value
  local users, err, code = core.users.getGroup("Fun Run", "cadets", 10)

  ...
end

return api
```

### getWithQuery

Get user record(s) based on specific query parameters.

```lua
core.users.getWithQuery(scope, query_params)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|scope|The application scope to be queried.|_String_|__Y__|
|query_tbl|A table with the query parameters (see below).|_Table_|__N__|

__Query Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|active|The active state of the user.|_Boolean_|__N__|
|country_code|A two letter country identifier.|_String_|__N__|
|email|The users email address.|_String_|__N__|
|group|The group name to search within.|_String_|__N__|
|username|The users username.|_String_|__N__|
|password|A hashed password (see also [hashPassword](#hashpassword)).|_String_|__N__|
|orderby|The sorting attributes. See __Orderby__ below.|_Table_|__N__|
|limit|Limit the records returned. See __Limit__ below.|_Number_ or _Table_|__N__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Orderby__

The `orderby` key should be a table filled with column = direction pairs. The direction can be either "ASC" for ascending order or "DESC" for descending order. See example below.

__Limit__

To limit the rows returned, supply a number value to the `limit` key. To offset the limit, supply a table array of number values. For example, to return rows 6-15, then `limit = {5, 10}`.


__Returns__

The records found as a table array, or __nil__, error, and error code. If `limit` is set to 1, then a single table with the user data is returned (not an array). If no query parameters are provided, returns users in the supplied scope, with a default limit of 100. 

__Example__

```lua
local users, err, code = core.users.getWithQuery("Space Race", {
  group = "cadets",
  active = true,
  orderby = { username = "ASC" },
  limit = 20
})
```

### getAndMerge

Get a user record, and merge multiple associated database and table quries related to the user into the users record.

```lua
core.users.getAndMerge(user_id, merge_tbl)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|merge_tbl|A table array of database queries. See __[mysql.selectMerge](/server/modules/mysql/#selectmerge)__|_String_|__Y__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Creating Associations__

To use this method properly, you must create additonal databases with tables. The tables that you wish to associate to the user must have a __user_id__ column filled with the users unique identifier. You can create the associations using the client-side or server-side __MySQL__ module.

__Returns__

A user record with the associated queries included as the `key` name assigned in the query table. See __[mysql.selectMerge](/server/modules/mysql/#selectmerge)__ for more information on how the query entries work.

__Errors__

If the user is not found the method will return __nil__, error, and error code. If the user is found but any associated database queries fail, the user record will still be returned with any successful associated queries. On failed associations, the `key` property of the failed database query will contain an `errors` key describing the errors.

__Example__

```lua
local users, err, code = core.users.getAndMerge("aad3eba3...", {
  {
    db = "locations",
    tbl = "spots",
    columns = { "longitude", "latitude" },
    limit = 10,
    orderby = { longitude = "DESC" },
    key = "spots"
  }
})
```

### sendConfirmationLink

Send an email to a newly registered user to confirm their email address. See also __[Confirmations](/server/modules/users/confirmations/)__.

```lua
core.users.sendConfirmationLink(user_id, options)
```

!!! warning "Mailgun Account Required"
    A valid __Mailgun__ account is required to use this method. See the __[Mailgun Config](/server/webmin/mailgun/)__ section for more information.

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique ID to send the confirmation link to.|_String_|__Y__|
|options|A table with email confirmation options (see below).|_Table_|__Y__|

__Confirmation Options Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|from_email|The senders email address. This is usually your address.|_String_|__Y__|
|subject|The confirmation email subject line.|_String_|__Y__|
|email_tpl|Identifier for a custom confirmation email template.|_String_|__N__|
|tpl_keys|Additional template keys for the custom confirmation email template.|_Table_|__N__|


__Returns__

A __String__ indicating the sent state from Mailgun, can be "OK" or "Failed", or __nil__, error, and error code.

_Note: If the user is not registered with an email address, this action will return an error._

__Example__

```lua
local res, err, code = core.users.sendConfirmationLink("0091633d...", {
  from_email = "code@develephant.com",
  subject = "Email confirmation"
})
```

See the __[Confirmations](/server/modules/users/confirmations/)__ section for more detailed information.

### sendPasswordResetLink

Send an email to a user with a special link to reset the users password. See also __[Password Reset](/server/modules/users/passwordreset/)__.

```lua
core.users.sendPasswordResetLink(to_email, scope, options)
```

!!! warning "Mailgun Account Required"
    A valid __Mailgun__ account is required to use this method. See the __[Mailgun Config](/server/webmin/mailgun/)__ section for more information.

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|to_email|The users email address to send the reset link to.|_String_|__Y__|
|scope|The application scope to be queried.|_String_|__Y__|
|options|A table with email reset options (see below).|_Table_|__Y__|

__Reset Options Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|from_email|The senders email address. This is usually your address.|_String_|__Y__|
|subject|The password reset email subject line.|_String_|__Y__|
|tpl_name|Identifier for a custom password reset email template.|_String_|__N__|
|tpl_keys|Additional template keys for the custom password reset email template.|_Table_|__N__|

__Returns__

A __boolean__ `true` value on success, or __nil__ and an error.

__Example__

```lua
local res, err = core.users.sendPasswordResetLink("me@home.com", "Fun Run", 
  {
    from_email = "myapp@domain.com",
    subject = "Password Reset Request",     
  }
)
```

See the __[Password Reset](/server/modules/users/passwordreset/)__ section for more detailed information.

### hashPassword

A utility method for hashing a plain text password. Generally used with __[getWithQuery](#getwithquery)__ or for rolling your own systems.

__Important Note__

All __User__ module methods that deal with passwords will automatically handle the password hashing. ___Do not double hash passwords or they will not work properly___. 

The server-side methods __[register](#register)__ and __[update](#update)__ automatically do password hashing and do not require this method.

```lua
core.users.hashPassword(plain_text)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|plain_text|A plain text password that will be hashed into a Users module compatiable version.|_String_|__Y__|

__Returns__

The hashed password as a hexadecimal __String__.

__Example__

```lua
local hashed_pw = core.users.hashPassword("tacos4Lunch!")

--hashed_pw will contain something like: 8b4c621decfdbb6b72c2cf179b3c344d
```