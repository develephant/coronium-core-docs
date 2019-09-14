Send email messages using the __[Mailgun](https://mailgun.com)__ messaging service.

!!! warning "Mailgun Account Required"
    A valid __Mailgun__ account and the proper configuration must be set up to use the __email__ module. See the __[Configuration](#configuration)__ section below. The __Mailgun__ messaging service allows you to send up to 10,000 free email messages a month.

!!! note "Screencast Available"
    Learn about sending email in a screencast format by __[Clicking here](/screencasts/#email-module)__.

### new

Create a new email message object.

```lua
core.email.new([key][, domain])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|key|Your Mailgun API key.|String|__N__|
|domain|Your verified Mailgun domain.|String|__N__|

!!! info "Default Credentials"
    If you have already set your __Mailgun__ credentials using the __[Webmin](/server/webmin/setup/)__ then these will be used by default, and you do not need to include the `key` and `domain` arguments.

__Returns__

A new email message object based on the __Mailgun__ credentials.

__Example__

```lua
local msg = core.email.new()
```

## Message Object

### to

The address(es) to send the message to. Multiple values must be seperated by a comma. ___This is a required value___.

```lua
msg:to( address[, addressN] )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|address|The recipient address(es).|String|

__Examples__

_Single address:_

```lua
--Address only
msg:to("user@email.com")

--With name
msg:to("Chris <user@email.com>")
```

_Multiple addresses:_

```lua
msg:to("user1@email.com", "Jim <user2@email.com>")
```

### from

The message sender address. ___This is a required value___.

```lua
msg:from( address )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|address|The sender address.|String|

__Example__

```lua
msg:from("sender@email.com")
```

### cc

The address(es) to "cc" the message to. Multiple values must be seperated by a comma.

```lua
msg:cc( address[, addressN] )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|address|The "cc" address(es).|String|

__Examples__

_Single address:_

```lua
--Address only
msg:cc("user@email.com")

--With name
msg:cc("Chris <user@email.com>")
```

_Multiple addresses:_

```lua
msg:cc("user1@email.com", "Jim <user2@email.com>")
```

### bcc

The address(es) to "bcc" the message to. Multiple values must be seperated by a comma.

```lua
msg:bcc( address[, addressN] )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|address|The "bcc" address(es).|String|

__Examples__

_Single address:_

```lua
--Address only
msg:bcc("user@email.com")

--With name
msg:bcc("Chris <user@email.com>")
```

_Multiple addresses:_

```lua
msg:bcc("user1@email.com", "Jim <user2@email.com>")
```

### replyTo

Sets a specific reply-to address. If not set, the sender address is the default.

```lua
msg:replyTo( address )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|address|A specific reply-to address.|String|

__Example__

```lua
msg:replyTo("Support <support@email.com>")
```

### subject

The subject line for the email. The default is "(no subject)" if not provided.

```lua
msg:subject( subject_str )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|subject_str|The subject title.|String|

__Example__

```lua
msg:subject("A Super Email")
```

### text

The plain text version of the message body.

```lua
msg:text( message_str )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|message_str|A plain string message.|String|

__Example__

```lua
msg:text("Here is your plain information.")
```

### html

The html version of the message body.

```lua
msg:html( message_html )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|message_html|An html string message.|String|

__Example__

```lua
msg:html("Here is your <b>bold</b> information.")
```

### tag

Sets a specific tag for the email. Can be used for tracking in Mailgun.

```lua
msg:tag( tag_str )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|tag_str|A string tag for tracking.|String|

__Example__

```lua
msg:tag("appuser")
```

### requireTls

Force Mailgun to use TLS when sending the message. Defaults to _false_.

```lua
msg:requireTls( bool )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|bool|Whether to enable TLS for this message.|Boolean|

__Example__

```lua
msg:requireTls(true)
```

### skipVerification

Skip certificate verification when using TLS. Defaults to _false_.

```lua
msg:skipVerification( bool )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|bool|Whether to skip verification in TLS.|Boolean|

__Example__

```lua
msg:skipVerification(true)
```

### testMode

Used for debugging in Mailgun. Defaults to _false_.

```lua
msg:testMode( bool )
```

__Parameters__

|Name|Description|Type|
|----|-----------|----|
|bool|Whether to enable test mode.|Boolean|

__Example__

```lua
msg:testMode(true)
```

### send

Send the final message via Mailgun.

```lua
msg:send()
```

__Parameters__

This method takes no parameters.

__Example__

```lua
local resp, err = msg:send()
```

## Configuration

To use the __Email__ module you will need a valid __[Mailgun](https://mailgun.com)__ account.

Once you have your account set-up you will need to provide your _Sending Domain_ and _Secret API Key_ from Mailgun to your Coronium Core system.

Using the __[Webmin](/server/webmin/setup/)__, navigate to the __Config__ section and enter the requested information in the __Mailgun Settings__ area. Click the __update__ button to add your credentials.

You can now use the __Email__ module for sending email.