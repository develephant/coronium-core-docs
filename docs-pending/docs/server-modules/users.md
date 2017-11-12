Provides methods to register users for your applications.



### get

```lua
core.users.get(user_id)
```

### update

```lua
core.users.update(user_id, update_params)
```

### delete

```lua
core.users.delete(user_id)
```

### create

!!! warning "Important Note"
    When creating users use the client-side __[create](/client-module/users/#create)__ method in the __Users__ module. This server-side method is for creating new users with web forms using the __[Pages](/pages-guide/api/)__ module.

```lua
core.users.create(params)
```

### updatePassword

```lua
core.users.updatePassword(user_id, new_password)
```

### hashPassword

A utility method.

```lua
core.users.hashPassword(plain_text_password)
```

## Email Confirmations
