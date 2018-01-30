The OAuth API is an extension of the server-side __[Users](/server/modules/users/api/)__ module. The following methods allow you to manage OAuth providers for a custom user. See the server-side __[Users](/server/modules/users/api/)__ module for more information on managing custom users.

The main use cases for the OAuth API are [Facebook Login](https://docs.coronalabs.com/plugin/facebook-v4a/login.html), [Google Sign-In](https://marketplace.coronalabs.com/plugin/google-sign-in), and [Devices as Users](http://127.0.0.1:8000/client/modules/users/creating/#devices-as-users).

!!! tip "Client-Side OAuth Users"
    Working with client-side __[OAuth API](/client/modules/users/oauth/)__ is the recommended way to work with OAuth users.

### getWithProvider

Get a user record based on an auth provider and immutable client ID.

```lua
core.users.getWithProvider(scope, provider, client_id)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|scope|The application scope the user belongs to.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|
|client_id|The immutable identifier returned by the auth provider.|_String_|__Y__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Returns__

A user record as a __table__, or __nil__, error, and error code. The record will also include the provider information in the users `oauth` key.

The `oauth` table contains the following keys

  - `client_id` (_string_)
  - `access_token` (_string_)
  - `access_token_expiry` (_number_)
  - `access_token_expired` (_boolean_)
  - `provider` (_string_)

__Example__

```lua
local api = core.api()

function api.getUser(input, scope)

  local user, err, code = core.users.getWithProvider(
    scope, 
    core.FACEBOOK, 
    "fb-id_1234")

  ...
end

return api
```

__Token Expiration__

If an access token and expiration has been added to the record, the token expiration state will be determined internally. You can check for expiration with the __boolean__ `access_token_expired` key. See also the __[accessTokenExpired](#accesstokenexpired)__ method.

__Example__

```lua
local api = core.api()

function api.getUser(input, scope)

  local user, err, code = core.users.getWithProvider(
    scope, 
    core.FACEBOOK, 
    "fb-id_1234")

  --check access token expiration
  if user.oauth.access_token_expired then
    --token has expired
  else
    --token is valid
  end

  ...

end

return api
```

### addAuthProvider

Link OAuth provider information to a custom user.

```lua
core.users.addAuthProvider(user_id, provider, info_tbl)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|
|info_tbl|Table of the provider information in key/value format (see below).|_Table_|__Y__|

__Info Table Keys__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|client_id|The immutable ID provided by the OAuth provider.|_String_|__Y__|
|access_token|OAuth provider access token, if any.|_String_|__N__|
|access_token_expiry|The lifetime of the access token as a UNIX timestamp, if any.|_Number_|__N__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

__Returns__

The added provider information as a __table__, or __nil__, error, and error code.

The returned table contains the following keys:

  - `client_id` (_string_)
  - `access_token` (_string_)
  - `access_token_expiry` (_number_)
  - `access_token_expired` (_boolean_)
  - `provider` (_string_)

__Example__

```lua
local res, err, code = core.users.addAuthProvider("aad3eba3...", core.FACEBOOK, {
  client_id = "fb-id-1234",
  access_token = "1234abcd",
  access_token_expiry = 1516647155
})
```

### getAuthProvider

Get OAuth provider information for a custom user.

```lua
core.users.getAuthProvider(user_id, provider)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|

__Returns__

The provider information as a __table__, or __nil__, error, and error code.

The returned table contains the following keys

  - `client_id` (_string_)
  - `access_token` (_string_)
  - `access_token_expiry` (_number_)
  - `access_token_expired` (_boolean_)
  - `provider` (_string_)

__Example__

```lua
local res, err, code = core.users.getAuthProvider("aad3eba3...", core.FACEBOOK)
```

### getAuthProviders

Get all OAuth providers information for a custom user.

```lua
core.users.getAuthProviders(user_id)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|

__Returns__

The provider information as a keyed __table__ by provider, with each providers information table, or __nil__, error, and error code.

The table returned has a structure like so:

```lua
{
  facebook = {
    client_id = "fb-id-1234",
    access_token = "1234abcd",
    access_token_expiry = 1516647155,
    access_token_expired = false
  },
  google = {
    client_id = "google-id-1234",
    access_token = "1234abcd",
    access_token_expiry = 1516640011,
    access_token_expired = true    
  },
  ...
}
```

__Example__

```lua
local res, err, code = core.users.getAuthProviders("aad3eba3...")
```

### updateAuthProvider

Update OAuth provider information for a custom user.

```lua
core.users.updateAuthProvider(user_id, provider, params)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|
|params|A table of key/value pairs of provider information.|_Table_|__Y__|

!!! warning "Important"
    __String values are automatically run through `mysql.escape`. Do not double-escape values.__

The following keys are updatable:

- `client_id` (_string_)
- `access_token` (_string_)
- `access_token_expiry` (_number_)

__Returns__

The updated provider information as a __table__ (see [getAuthProvider](#getauthprovider)), or __nil__, error, and error code.

__Example__

```lua
local res, err, code = core.users.updateAuthProvider("aad3eba3...", core.FACEBOOK, 
{
  access_token = "1234efgh",
  access_token_expiry = 1516663152
})
```

### removeAuthProvider

Remove an OAuth provider from a custom user.

```lua
core.users.removeAuthProvider(user_id, provider)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|

__Returns__

The `user_id` as a __string__, or __nil__, error, and error code.

__Example__

```lua
local res, err, code = core.users.removeAuthProvider("aad3eba3...", core.FACEBOOK)
```

### removeAuthProviders

Remove all OAuth providers from a custom user.

```lua
core.users.removeAuthProviders(user_id)
```

!!! warning
    This method clears ___all___ of the providers for the given user ID.

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|

__Returns__

The `user_id` as a __string__, or __nil__, error, and error code.

__Example__

```lua
local res, err, code = core.users.removeAuthProviders("aad3eba3...")
```

### accessTokenExpired

Conditionally check if a users access token has expired for the specified provider.

```lua
core.users.accessTokenExpired(user_id, provider)
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|

__Returns__

A __boolean__ value based on whether the access token has expired. Will be `true` is the token has expired, or `false` if the access token is still valid.

!!! note
    If the user does not have the specified provider linked to their account, the result will always be __true__.

__Example__

```lua
if core.users.accessTokenExpired("aad3eba3...", core.FACEBOOK) then
  --token has expired
else
  --token is still valid
end
```

### updateTokenExpiry

Update the provider token expiration time. This is a convenience method, see also __[updateAuthProvider](#updateauthprovider)__.

```lua
core.users.updateTokenExpiry(user_id, provider, expiry[, token])
```

__Parameters__

|Name|Description|Type|Required|
|---|-----------|----|--------|
|user_id|The users unique identifier.|_String_|__Y__|
|provider|The OAuth provider constant (see __[OAuth Constants](#oauth-constants)__).|_Const_|__Y__|
|expiry|The new access token expiration time as a UNIX timestamp.|_Number_|__Y__|
|token|Optionally update the access token string as well.|_String_|__N__|

__Returns__

The updated provider information as a __table__ (see [getAuthProvider](#getauthprovider)), or __nil__, error, and error code.

__Example__

```lua
local res, err, code = core.users.updateTokenExpiry(
  "aad3eba3...", 
  core.FACEBOOK, 
  1516663152)
```

## OAuth Constants

Used for the `provider` parameter in OAuth methods.

|Constant|Description|See Also|
|--------|-----------|--------|
|core.FACEBOOK|Marks a provider as Facebook.|__[Facebook Login](https://docs.coronalabs.com/plugin/facebook-v4a/login.html)__|
|core.GOOGLE|Marks a provider as Google.|__[Google Sign-In](https://marketplace.coronalabs.com/plugin/google-sign-in)__|
|core.OPENUDID|Marks a provider as OpenUDID|__[Devices As Users](/client/modules/users/creating/#devices-as-users)__|

