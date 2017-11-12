You can _optionally_ require a user to confirm their registration via email by using the email confirmation feature of the __Users__ module.

!!! warning "Mailgun Account Required"
    A valid __Mailgun__ account and the proper configuration must be set up to use the email confirmation feature. See the __[Mailgun Config](/webmin-guide/mailgun/)__ section for more information.

## Confirmation Table

When using the confirmation feature, the following parameters can be supplied in the __confirmation__ table. Not all parameters are required.

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|from_email|The senders email address. This is usually your address.|_String_|__Y__|
|subject|The registration email subject line.|_String_|__Y__|
|email_tpl|Identifier for a custom registration email template.|_String_|__N__|
|tpl_keys|Additional template keys for the registration email template.|_Table_|__N__|

## Client-Side Example

You initialize the creation and confirmation using the client-side __[core.users.create](/client-module/users/#create)__ method.

### Default Confirmation

```lua
core.users.create({
  username = "SuperUser",
  password = "1234abcd",
  email = "newuser@somemail.com",
  confirmation = {
    from_email = "register@funrun.com",
    subject = "Please confirm your registration.",
  }
}, onUserCreate)
```

!!! info
    Plain text passwords are hashed before being sent to the server. Do not try to hash the passwords yourself.

## Default Template

By default the following email template is sent to the users email. The only required template key is the __{\* confirm_link \*}__ which is replaced automatically with the proper link to confirm the user.

!!! fail "Required Key"
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

To create a custom email template, you create, and place your custom template file in the __/home/coronium/templates/registration/__ directory, and pass a reference to the template file in the __confirmation.email_tpl__ parameter.

At a minimum you must include the __{* confirm_link \*}__ template key in your custom template for the auto-generated confirmation link, but you can also supply other template keys by passing a table of key/value pairs to the __confirmation.tpl_keys__ parameter.

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

### User Create Code

```lua
core.users.create({
  username = "SuperUser",
  password = "1234abcd",
  email = "newuser@somemail.com",
  confirmation = {
    from_email = "register@funrun.com",
    subject = "Please confirm your registration.",
    email_tpl = "custom_email.tpl",
    tpl_keys = {
      username = "Timmy",
      app_name = "FunRun"
    }
  }
}, onUserCreate)
```

__Result__

```text
Hello Timmy,

Thank you for signing up to use the FunRun application.

Please click the following link to confirm your registration:

http://your.coronium.host/_confirmation?code=<generated-confirmation-code>

Have a nice day.
```

### Important Notes

When using the email confirmation feature you must provide both the __email__ key and the __confirmation.from_email__ key or the confirmation will not be sent.

Do not add a __confirm_link__ key to the __confirmation.tpl_keys__ entry. Just be sure to include it in your custom template and it will be populated automatically with the correct confirmation link when the email is sent.

## Confirmation Page

Once the user clicks the confirmation link in the registration email, they will be taken to the confirmation landing page. 

When the user is confirmed they will be marked as _active_ in the __users__ database.

To customize the landing page, you can edit the following resources.

Template: __/home/coronium/pages/_coronium/registration/confirmation.tpl__

Lua Code: __/home/coronium/pages/_coronium/registration/confirmation.lua__
