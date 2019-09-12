To update a users data, you must pass a __user_id__ and an __update__ table with the values you wish to update. On a successful update, you will recieve the newly updated record in the event response.

!!! note
    You can update multiple values in the update table of the method.

The following keys can be updated on the users record:

- `username` (_string_)
- `password` (_string_)
- `email` (_string_)
- `group` (_string_)
- `extra` (_table_)
- `active` (_boolean_)

!!! info "OAuth Users"
    To update a users OAuth provider information, see the __[updateAuthProvider](/client/modules/users/oauth/#updateauthprovider)__ method.

__Example__

```lua
local function onUserUpdate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id) --result contains updated object
  end
end

core.users.update({
  user_id = "289bc36e-0df7-44df-99b1-a6543c6f45eb",
  update = {
    email = "app@email.com"
  }
}, onUserUpdate)
```

## Username

To update a users name, pass the __username__ key to the __update__ table. If a user with the same name already exists in the same application scope, the record will not be updated and an error will be returned with a status of 600.

```lua
local function onUserUpdate( evt )
  if evt.error then
    if evt.status == 600 then
      --user exists, handle appropriately
    else
      print(evt.error)
    end
  else
    print(evt.result.user_id)
  end
end

core.users.update({
  user_id = "289bc36e-0df7-44df-99b1-a6543c6f45eb",
  update = {
    username = "Sammy"
  }
}, onUserUpdate)
```

## Password

To update a users password, pass the __password__ key to the __update__ table. 

!!! warning "Important"
    Passwords are automatically hashed, do not hash the password yourself.

```lua
local function onUserUpdate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id)
  end
end

core.users.update({
  user_id = "289bc36e-0df7-44df-99b1-a6543c6f45eb",
  update = {
    password = "abcd"
  }
}, onUserUpdate)
```

## Extra Metadata

To add, update, or remove a users "extra" metadata, pass the __extra__ table to the __update__ table.

!!! tip
    You can add, update, and remove multiple keys in the same call.

### Update/Add Key

```lua
local function onUserUpdate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id)
  end
end

core.users.update({
  user_id = "289bc36e-0df7-44df-99b1-a6543c6f45eb",
  update = {
    extra = {
      color = "Red",
      winner = true
    }
  }
}, onUserUpdate)
```

### Removing a Key

To clear and remove a key from the users "extra" metadata, you must pass the `core.users.NULL` constant to the key.

```lua
local function onUserUpdate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result.user_id)
  end
end

core.users.update({
  user_id = "289bc36e-0df7-44df-99b1-a6543c6f45eb",
  update = {
    extra = {
      winner = core.users.NULL --remove 'winner' key
    }
  }
}, onUserUpdate)
```

## Active State

To change the "active" status of a user, pass the __active__ key as a boolean value.

```lua
local function onUserUpdate( evt )
  if evt.error then
    print(evt.error)
  else
    print(evt.result)
  end
end

core.users.update({
  user_id = "289bc36e-0df7-44df-99b1-a6543c6f45eb",
  update = {
    active = false
  }
}, onUserUpdate)
```