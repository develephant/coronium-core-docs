Various add-ons you can install on your server. Requires __Coronium Core 2.4.1__ or higher.

___All add-ons are unsupported beyond installation.___

## <i class="fab fa-php"></i> PHP-FPM

Installs the PHP-FPM 7.x executable and some standard packages. 

### Installation

!!! danger "Root User Required"
    You must be logged in as __root__ on DigitalOcean or __ubuntu__ on Amazon EC2 to run the installer.

Paste the following one-liner into your terminal to start the installation:

```
wget https://s3.amazonaws.com/coronium-core-addons/php/php.sh && sudo bash php.sh
```

### Usage

Place `.php` files in the __/home/coronium/php__ directory via SFTP as the __coronium__ user.

PHP is only accessable internally via server-side API using the `core.run` or `core.network.request` methods.

Using `core.run`

```lua
local api = core.api()

function api.phpinfo(input)

  --== Runs the PHP executable directly
  local resp, err = core.run("php /home/coronium/php/info.php")

  if not resp then
    return core.error(err)
  end

  return resp

end

return api
```

Using `core.network.request`

```lua
local api = core.api()

function api.phpinfo(input)

  --== Runs internal web server request
  local resp, err = core.network.request("http://127.0.0.1:80/info.php", {
    method = "GET"
  })

  if not resp then
    return core.error(err)
  end

  return resp.body

end

return api
```

## <i class="fab fa-node"></i> NodeJS

_Coming Soon_

### Installation

### Usage