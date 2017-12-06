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