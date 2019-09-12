Require a user to confirm their email by using the __[users.sendConfirmationLink](/server/modules/users/api/#sendconfirmationlink)__ method of the __Users__ module. 

When a confirmation email is sent, the user is in a "pending" state. You can view __Pending Users__ in the __Users__ section of the __[Webmin](/server/webmin/setup/)__.

!!! warning "Mailgun Account Required"
    A valid __Mailgun__ account and the proper configuration must be set up to use the email confirmation feature. See the __[Mailgun Config](/server/webmin/mailgun/)__ section for more information.

## Confirmation Options

The following keys are available to pass to the options parameter of the __[users.sendConfirmationLink](/server/modules/users/api/#sendconfirmationlink)__ method depending on whether you are using the default or a custom template.

|Name|Description|Type|Required|
|---|-----------|----|--------|
|from_email|The senders email address. This is usually your address.|_String_|__Y__|
|subject|The confirmation email subject line.|_String_|__Y__|
|email_tpl|Identifier for a custom confirmation email template.|_String_|__N__|
|tpl_keys|Additional template keys for the custom confirmation email template.|_Table_|__N__|

## Server-Side Example

You need a registered user to send a confirmation. Generally you create a user with the __[user.register](/server/modules/users/api/#register)__ method, making sure to supply an email address, and then follow up with the __[users.sendConfirmationLink](/server/modules/users/api/#sendconfirmationlink)__ method.

### Default Confirmation

```lua
function api.createAndConfirm(input)

  -- Register a user
  local user_id, err, code = core.users.register("Wolly", nil, "Space Race", {
    email = "wolly@home.com"
  })

  if not user_id then
    return core.error(err, code)
  end
  
  -- Send the confirmation
  local res, err, code = core.users.sendConfirmationLink(user_id, {
    from_email = "yourapp@domain.com",
    subject = "Please confirm your registration.",
  })

  if not res then
    return core.error(err, code)
  end
  
  return res

end
```

### Response

On success, the __sendConfirmationLink__ method will return a __String__ indicating the sent state from Mailgun, which can be "OK" or "Failed". Otherwise a __nil__, error, and error code is returned.

## Default Template

By default the following email template is sent to the users email. The only required template key is the __{\* confirm_link \*}__ which is replaced automatically with the proper link to confirm the user.

!!! fail "Required Template Key"
    If you do not include the __{\* confirm_link \*}__ template key in the email template, the user will have no way to confirm the registration.

You can edit the default template messaging if you wish, but to add additional template keys you must create a custom template, which is explained in the next section.

Template Location: __/home/coronium/templates/registration/email.tpl__

```text
Hello,

You have requested to sign up for our app. 

Please confirm your registration by clicking the link below:

{* confirm_link *}

Thank you.
```

## Custom Templates

To create a custom email template, you create, and place your custom template file in the __/home/coronium/templates/registration/__ directory, and pass a reference to the template file in the `email_tpl` parameter of the `options` table.

At a minimum you must include the __{* confirm_link \*}__ template key in your custom template for the auto-generated confirmation link, but you can also supply other template keys by passing a table of key/value pairs to the `tpl_keys` parameter of the `options` table.

!!! tip ""
    You can create as many custom email templates as you wish.

### Template Example

Template Location: __/home/coronium/templates/registration/custom_email.tpl__

```text
Hello {{ username }},

Thank you for signing up to use the {{ app_name }} application.

Please click the following link to confirm your registration:

{* confirm_link *}

Have a nice day.
```

### Custom Confirmation

```lua
function api.createAndConfirm(input)

  -- Register a user
  local user_id, err, code = core.users.register("Wolly", nil, "Space Race", {
    email = "wolly@home.com"
  })

  if not user_id then
    return core.error(err, code)
  end
  
  -- Send the confirmation
  local res, err, code = core.users.sendConfirmationLink(user_id, {
    from_email = "yourapp@domain.com",
    subject = "Please confirm your registration.",
    email_tpl = "custom_email.tpl",
    tpl_keys = {
      username = "Wolly",
      app_name = "Space Race"
    }
  })

  if not res then
    return core.error(err, code)
  end
  
  return res

end
```

__Result__

```text
Hello Wolly,

Thank you for signing up to use the Space Race application.

Please click the following link to confirm your registration:

http://your.coronium.host/_confirmation?code=<generated-confirmation-code>

Have a nice day.
```

### Important Notes

Do not add a __confirm_link__ key to the `tpl_keys` parameter. Just be sure to include it in your custom template and it will be populated automatically with the correct confirmation link when the email is sent.

## Confirmation Page

Once the user clicks the confirmation link in the registration email, they will be taken to the confirmation landing page. 

When the user is confirmed they will be marked as _active_ in the __users__ database.

To customize the landing page, you can edit the following resources.

 - Template: __/home/coronium/pages/_coronium/registration/confirmation.tpl__
 - Lua Code: __/home/coronium/pages/_coronium/registration/confirmation.lua__

See the __[Pages](/server/modules/pages/usage/)__ usage guide for information about how to work with these files. 
