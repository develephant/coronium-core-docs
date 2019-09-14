Place `.php` files in the __/home/coronium/php__ directory via SFTP as the `coronium` user.

PHP is only accessable internally via the following server-side API.

!!! note ""
    The PHP module is only available in Coronium Core 2.7 and above. 

## API

### call

Call a PHP script and return the result as a Lua table.

```lua
core.php.call(php_page, data_tbl)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|php_page|The PHP script to call, relative to the PHP directory.|_String_|__Y__|
|data_tbl|A table of key/value parameters (will be converted to JSON).|_Table_|__Y__|

__Returns__

On success, returns a keyed __table__. Otherwise, __nil__ and an error.

__PHP Example__

```php
<?php
  $input = json_decode(file_get_contents('php://input'));
  $greeting = 'Hello ' . $input->{"username"} . '!';
  $result = array("greeting" => $greeting);

  print(json_encode($result));
?>
```

__API Example__

```lua
local result, err = core.php.call('greeting.php', { 
  username = "Donna" 
})

if not result then
  core.log(err)
else
  core.log(result.greeting) -- Hello Donna
end
```

---

### postForm

Post form values (as a table) to a PHP page using the "POST" method.

Only supports the "x-www-form-urlencoded" form type.

```lua
core.php.postForm(php_page, form_tbl)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|php_page|The PHP script to call, relative to the PHP directory.|_String_|__Y__|
|form_tbl|A table of key/value form parameters.|_Table_|__Y__|

__Returns__

On success, returns a __table__ with response keys (see below). Otherwise, __nil__ and an error.

__Response Table Keys__

|Name|Description|Type|
|----|-----------|----|
|body|The response body returned by the request, if any.|_String_|
|headers|A table of response headers from the request.|_Table_|
|status|The HTTP response status of the request.|_Number_|

__PHP Example__

```php
<?php
  $username = $_POST["username"];
  print("Welcome " . $username); 
?>
```

__API Example__

```lua
local resp, err = core.php.postForm('form.php', {
  username = "Tim"
})

if not resp then
  core.log(err)
else
  core.log(resp.body) -- Welcome Tim
end
```

---

### post

Post raw content to a PHP page using the "POST" method. Defaults to "text/plain" content type.

```lua
core.php.post(php_page, body[, content_type])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|php_page|The PHP script to call, relative to the PHP directory.|_String_|__Y__|
|body|The content body to post to the PHP page.|_String_|__Y__|
|content_type|The MIME content type. Defaults to "text/plain"|_String_|__N__|

__Returns__

On success, returns a __table__ with response keys (see below). Otherwise, __nil__ and an error.

__Response Table Keys__

|Name|Description|Type|
|----|-----------|----|
|body|The response body returned by the request, if any.|_String_|
|headers|A table of response headers from the request.|_Table_|
|status|The HTTP response status of the request.|_Number_|

__PHP Example__

```php
<?php
  $input = file_get_contents('php://input');
  print($input);
?>
```

__API Example__

```lua
local resp, err = core.php.post('post.php', 'Here is some body text')

if not resp then
  core.log(err)
else
  core.log(resp.body) -- Here is some body text
end
```

---

### get

Call a PHP page using the "GET" method, with optional query string (as a table).

```lua
core.php.get(php_page[, query_tbl])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|php_page|The PHP script to call, relative to the PHP directory.|_String_|__Y__|
|query_tbl|A table of key/value of query parameters.|_Table_|__N__|

__Returns__

On success, returns a __table__ with response keys (see below). Otherwise, __nil__ and an error.

__Response Table Keys__

|Name|Description|Type|
|----|-----------|----|
|body|The response body returned by the request, if any.|_String_|
|headers|A table of response headers from the request.|_Table_|
|status|The HTTP response status of the request.|_Number_|

__PHP Example__

```php
<?php
  $username = $_GET["username"];
  print("Hello " . $username); 
?>
```

__API Example__

```lua
local resp, err = core.php.get('get.php', {
  username = "Jenny"
})

if not resp then
  core.log(err)
else
  core.log(resp.body) -- Hello Jenny
end
```

---

### request

Perform a raw request to a PHP page. Supports "POST", "GET", "PUT", "DELETE", etc.

```lua
core.php.request(php_page, params)
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|php_page|The PHP script to call, relative to the PHP directory.|_String_|__Y__|
|params|A table of options for the request. See __Params Table Keys__ below.|_Table_|__Y__|

__Params Table Keys__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|method|The HTTP method for the request ("GET", "POST", etc.).|_String_|__Y__|
|headers|A table of headers to send with the request.|_Table_|__N__|
|body|A string body to send with a "POST" or "PUT" request.|_String_|__N__|

__Returns__

On success, returns a __table__ with response keys (see below). Otherwise, __nil__ and an error.

__Response Table Keys__

|Name|Description|Type|
|----|-----------|----|
|body|The response body returned by the request, if any.|_String_|
|headers|A table of response headers from the request.|_Table_|
|status|The HTTP response status of the request.|_Number_|
