## Creating Users

```lua
local api = core.api()

function api.createAuthUser(input, scope)

  --check if user already exists with provider
  local user, err = core.users.getWithProvider(
    scope, 
    core.FACEBOOK, 
    input.client_id)

  if not user then --a user does not exist

    --Create a custom anonymous Coronium user.
    --On success marks a "join" and "login" event.
    local user_id, err = core.users.register(nil, nil, scope, {
      login = true
    })

    if not user_id then
      core.log(err)
    else

      --Link user with OAuth provider info
      local res, err = core.users.addAuthProvider(user_id, core.FACEBOOK, {
        client_id = input.client_id,
        access_token = input.access_token,
        access_token_expiry = input.access_token_expires
      })

      if not res then
        core.log(err)
      else
        -- user is created and linked with auth provider.
      end
    end

  end

  return true

end

return api
```

## Logging In

```lua
local api = core.api()

function api.loginAuthUser(input, scope)

  local user, err = core.users.login({
    scope = scope,
    provider = core.FACEBOOK,
    client_id = input.client_id
  })

  if not user then
    --user does not exist, create one (see Creating Users above).
    return api.createAuthUser(input, scope)
  else
    --user exists and a "login" event has been marked.

    --check and update access_token if needed
    if user.oauth.acess_token_expired then

      local res, err = core.users.updateAuthProvider(
        user.user_id, 
        core.FACEBOOK, 
        {
          access_token = input.access_token,
          access_token_expiry = input.access_token_expires
        })

    end

    return user
  end
end


return api
```
