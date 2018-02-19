__Examples__

_Default Template_

```lua
local res, err = core.users.sendPasswordReset("me@home.com", "Fun Run", 
  {
    from_email = "myapp@domain.com",
    subject = "Password Reset Request",     
  }
)
```

_Custom Template_

```lua
local res, err = core.users.sendPasswordReset("me@home.com", "Fun Run"
  {
    from_email = "myapp@domain.com",
    subject = "Password Reset Request",
    --custom template meta
    tpl_name = "my_email_reset.tpl",
    tpl_keys = {
      app_name = "Fun Run"
    }
  }
)
```