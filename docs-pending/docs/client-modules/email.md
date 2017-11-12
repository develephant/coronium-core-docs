
Send email messages using the __[Mailgun](https://mailgun.com)__ messaging service.

!!! warning "Mailgun Account Required"
    A valid __Mailgun__ account and the proper configuration must be set up to use the __email__ module. See the __[Configuration](#configuration)__ section below.

!!! info ""
    The __Mailgun__ messaging service allows you to send up to 10,000 free email messages a month.

### send

```lua
core.email.send(data_params, listener)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|data_params|The data parameters for the call.|_Table_|__Y__|
|listener|The api listener callback function.|_Function_|__Y__|

__Data Params__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|to|The address(es) to send the message to. Multiple values must be seperated by a comma.|_String_|__Y__|
|from|The message sender address.|_String_|__Y__|
|subject|The subject line for the email. The default is "(no subject)" if not provided.|_String_|__Y__|
|text|The plain text version of the message body.|_String_|__Y__|
|html|The html version of the message body.|_String_|__N__|
|replyTo|Sets a specific reply-to address. If not set, the sender address is the default.|_String_|__N__|
|cc|The address(es) to "cc" the message to. Multiple values must be seperated by a comma.|_String_|__N__|
|bcc|The address(es) to "bcc" the message to. Multiple values must be seperated by a comma.|_String_|__N__|
|tag|Sets a specific tag for the email. Can be used for tracking in Mailgun.|_String_|__N__|
|requireTls|Force Mailgun to use TLS when sending the message. Defaults to _false_.|_Boolean_|__N__|
|skipVerification|Skip certificate verification when using TLS. Defaults to _false_.|_Boolean_|__N__|
|testMode|Used for debugging in Mailgun. Defaults to _false_.|_Boolean_|__N__|

## Configuration

How to configure your Mailgun account.