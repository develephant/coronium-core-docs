The OAuth API is an extension of the __[Users API](/client/modules/users/api/)__ and provides methods to manage OAuth information for a client-side created user. 

Linking an OAuth provider allows you to log in a user with either a username and password, or OAuth credentials.

The main use cases for the OAuth API are [Facebook Login](https://docs.coronalabs.com/plugin/facebook-v4a/login.html), [Google Sign-In](https://marketplace.coronalabs.com/plugin/google-sign-in), and [Devices as Users](http://127.0.0.1:8000/client/modules/users/creating/#devices-as-users).

### addAuthProvider

Link OAuth provider information to a user.

```lua
core.users.addAuthProvider(auth_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|auth_tbl|The auth table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Auth Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|
|auth|Table of the provider information in key/value format (see below).|_Table_|__Y__|

__Auth Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|client_id|The immutable ID provided by the OAuth provider.|_String_|__Y__|
|access_token|OAuth provider access token, if any.|_String_|__N__|
|access_token_expiry|The lifetime of the access token as a UNIX timestamp, if any.|_Number_|__N__|

__Event Response__

On success, the __result__ will hold the added provider information as a __table__.

The returned table contains the following keys:

  - `client_id` (_string_)
  - `access_token` (_string_)
  - `access_token_expiry` (_number_)
  - `access_token_expired` (_boolean_)
  - `provider` (_string_)

__Example__

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error)
  else
    local oauth_info = evt.result 
  end
end

core.users.addAuthProvider({
  user_id = "aad3eba3...",
  provider = core.FACEBOOK,
  auth = {
    client_id = "fb-id-1234",
    access_token = "1234abcd",
    access_token_expiry = 1516647155
  }
}, apiResponse)
```

### getAuthProvider

Get OAuth provider information for a user.

```lua
core.users.getAuthProvider(auth_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|auth_tbl|The auth table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Auth Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|

__Event Response__

On success, the __result__ will hold the provider information as a __table__.

The returned table contains the following keys

  - `client_id` (_string_)
  - `access_token` (_string_)
  - `access_token_expiry` (_number_)
  - `access_token_expired` (_boolean_)
  - `provider` (_string_)

__Example__

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error)
  else
    local oauth_info = evt.result 

    --maybe check for expired token
    if oauth_info.access_token_expired then
      --token expired
    else
      --token valid
    end
  end
end

core.users.getAuthProvider({
  user_id = "aad3eba3...",
  provider = core.FACEBOOK
}, apiResponse)
```

### updateAuthProvider

Update OAuth provider information for a user.

```lua
core.users.updateAuthProvider(auth_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|auth_tbl|The auth table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Auth Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|
|update|A table of key/value pairs of provider information.|_Table_|__Y__|

The following keys are updatable:

- `client_id` (_string_)
- `access_token` (_string_)
- `access_token_expiry` (_number_)

__Event Response__

On success, the __result__ will hold the updated provider information as a __table__ (see [getAuthProvider](#getauthprovider)).

__Example__

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error)
  else
    local oauth_info = evt.result 
  end
end

core.users.updateAuthProvider({
  user_id = "aad3eba3...",
  provider = core.FACEBOOK,
  update = {
    access_token = "1234abcd",
    access_token_expiry = 1516647155
  }
}, apiResponse)
```

### removeAuthProvider

Remove an OAuth provider from a user.

```lua
core.users.removeAuthProvider(auth_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|auth_tbl|The auth table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Auth Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|

__Event Response__

On success, the __result__ will hold the `user_id` key as a __string__ value.

__Example__

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error)
  else
    local user_id = evt.result.user_id 
  end
end

core.users.removeAuthProvider({
  user_id = "aad3eba3...",
  provider = core.FACEBOOK
}, apiResponse)
```

### accessTokenExpired

Conditionally check if a users access token has expired for the specified provider.

```lua
core.users.accessTokenExpired(auth_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|auth_tbl|The auth table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Auth Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|

__Event Response__

On success, the __result__ will hold the `expired` key as a __boolean__ value.

__Example__

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error)
  else
    if evt.result.expired then
      --token is expired
    else
      --token is valid
    end
  end
end

core.users.accessTokenExpired({
  user_id = "aad3eba3...",
  provider = core.FACEBOOK
}, apiResponse)
```

### updateTokenExpiry

Update the provider token expiration time. This is a convenience method, see also __[updateAuthProvider](#updateauthprovider)__.

```lua
core.users.updateTokenExpiry(auth_tbl, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|auth_tbl|The auth table for the call (see below).|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Auth Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|
|expiry|The new access token expiration time as a UNIX timestamp.|_Number_|__Y__|
|token|Optionally update the access token string as well.|_String_|__N__|

__Event Response__

On success, the __result__ will hold the updated provider information as a __table__ (see [getAuthProvider](#getauthprovider)).

__Example__

```lua
local function apiResponse( evt )
  if evt.error then
    print(evt.error)
  else
    local oauth_info = evt.result 
  end
end

core.users.updateTokenExpiry({
  user_id = "aad3eba3...",
  provider = core.FACEBOOK,
  expiry = 1516647155
}, apiResponse)
```

## OAuth Constants

Used for the `provider` parameter in OAuth methods.

|Constant|Description|See Also|
|--------|-----------|--------|
|core.FACEBOOK|Marks a provider as Facebook.|__[Facebook Login](https://docs.coronalabs.com/plugin/facebook-v4a/login.html)__|
|core.GOOGLE|Marks a provider as Google.|__[Google Sign-In](https://marketplace.coronalabs.com/plugin/google-sign-in)__|
|core.OPENUDID|Marks a provider as OpenUDID|__[Devices As Users](/client/modules/users/creating/#devices-as-users)__|