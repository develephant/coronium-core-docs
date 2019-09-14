If you need to have a user reset their password, you can do this with the server-side __[users.sendPasswordResetLink](/server/modules/users/api/#sendpasswordresetlink)__ method, which will send an email with a password reset link that will allow the user to update the password via a form on the web.

!!! warning "Mailgun Account Required"
    A valid __Mailgun__ account and the proper configuration must be set up to use the password reset feature. See the __[Mailgun Config](/server/webmin/mailgun/)__ section for more information.

## Reset Options

The following keys are available to pass to the `options` parameter of the __[users.sendPasswordResetLink](/server/modules/users/api/#sendpasswordresetlink)__ method depending on whether you are using the default or a custom template.

|Name|Description|Type|Required|
|---|-----------|----|--------|
|from_email|The senders email address. This is usually your address.|_String_|__Y__|
|subject|The password reset email subject line.|_String_|__Y__|
|tpl_name|Identifier for a custom password reset email template.|_String_|__N__|
|tpl_keys|Additional template keys for the custom password reset email template.|_Table_|__N__|

## Default Reset

The following example will send a password reset email using the default template.

### Code Example

The only `options` parameter table keys required are the `from_email` and `subject`.

```lua
local res, err = core.users.sendPasswordResetLink("user@home.com", "Fun Run", 
  {
    from_email = "myapp@domain.com",
    subject = "Password Reset Request",     
  }
)
```

### Default Template

By default the following email template is sent to the users email. The only required template key is the __{\* reset_link \*}__ which is replaced automatically with the proper link to password reset form.

!!! fail "Required Template Key"
    If you do not include the __{\* reset_link \*}__ template key in the email template, the user will have no way to get to the reset form.

You can edit the default template messaging if you wish, but to add additional template keys you must create a custom template, which is explained in the next section.

Template Location: __/home/coronium/templates/password/email.tpl__

```text
Hello,

You have requested a password reset for our app. 

Please click the following link to reset your password:

{* reset_link *}

Thank you.
```

## Custom Reset

The following example will send a password reset email using a custom template.

To use a custom email template, you create, and place your custom template file in the __/home/coronium/templates/password/__ directory, and pass a reference to the template file in the `tpl_name` parameter.

At a minimum you must include the __{* reset_link \*}__ template key in your custom template for the auto-generated password reset link, but you can also supply other template keys by passing a table of key/value pairs to the `tpl_keys` parameter.

!!! tip ""
    You can create as many custom email templates as you wish.

### Code Example

The `from_email`, `subject`, `tpl_name`, and `tpl_keys` are all required on the `options` parameter table to use a custom template.

```lua
local res, err = core.users.sendPasswordResetLink("user@home.com", "Fun Run"
  {
    from_email = "myapp@domain.com",
    subject = "Password Reset Request",
    --custom template meta
    tpl_name = "custom_reset.tpl",
    tpl_keys = {
      username = "Carol",
      app_name = "Fun Run"
    }
  }
)
```

### Custom Template

Template Location: __/home/coronium/templates/password/custom_reset.tpl__

```text
Hello {{ username }},

You have requested a password reset for the {{ app_name }} application. 

Please click the following link to reset your password:

{* reset_link *}

Have a nice day.
```

__Result__

```text
Hello Carol,

You have requested a password reset for the Fun Run application. 

Please click the following link to reset your password:

http://your.coronium.host/_resetpassword?code=<generated-reset-code>

Have a nice day.
```

### Important Notes

Do not add a __reset_link__ key to the `tpl_keys` entry. Just be sure to include it in your custom template and it will be populated automatically with the correct password reset link when the email is sent.

## Reset Form Page

Once the user clicks the confirmation link in the password reset email, they will be taken to the password reset form page. 

To customize the reset form page, you can edit the following resources.

 - Template: __/home/coronium/pages/_coronium/password/password_form.tpl__
 - Lua Code: __/home/coronium/pages/_coronium/password/password_form.lua__
 - JS Code: __/home/coronium/pages/_coronium/password/password_submit.js__

See the __[Pages](/server/modules/pages/usage/)__ usage guide for information about how to work with these files. 

## Expiring Reset links

When a password reset email is sent, a database entry is added to the administration database.

If you're going to be using password resets, it is highly recommended you create a recurring Job using the __[Jobs Service](/coronium-core-docs/server/modules/jobs/guide/)__ to clean up expired password resets so the database does not grow too large.

When a user successfully updates their password, the entry is automatically removed from the database. So this Job only needs to clean up failed email sends and/or old reset links.

A special method called `users.flushExpiredPasswordResets` is available for use in Jobs, and will clear any password reset links that are over a day old.

You can make this clean up happen at longer intervals, depending how you set up the Job.

### Create Expiry Job

Using the Coronium __[Webmin]()__, go to the __Jobs Service__ section and click __New Job__.

You can use whatever values you'd like for your Job, but as an example:

 - __Job Name:__ CleanPasswordResets
 - __Job Path:__ admin/pw_reset_cleanup.lua
 - __Job Interval:__ 3600
 - __Run Once:__ OFF
 - __Active:__ ON

This Job will run every hour. Click the __Create__ button.

You will be taken to the Job code editor. Use the following Job code to clear links that are older than a day:

```lua
local job = core.job()

function job.run()
  core.users.flushExpiredPasswordResets()
  return job.OK
end

return job
```

When a user clicks on a link that no longer exists, they will be notified, at which point that can make another password reset request.

