The following explains how to access data in an asynchronous manner via AJAX using the Pages module.

## Page Response Module

You will need to create a "response" module that will placed in the same directory where your display page(s) reside. This module can talk to Coronium Core modules, and then you'll send back the responses.

__Directory Structure__

```text
pages/
  mypage.html
  pageapi.lua
```

In the `pageapi.lua` we set up methods to respond to the AJAX page requests. The passed in values from an AJAX call are in the `page.form` object when using the POST method.

!!! note "POST Method"
    For ease of use, you should always make your AJAX calls using the POST method.

### pageapi.lua

```lua
local page = core.pages.new()

--=============================================================================
--== Response Methods
--=============================================================================
local function getTimestamp(input)

  local res, err = core.mysql.timestamp(input.time)

  if not res then
    -- Something went wrong, send 500 error
    page.response("Error", nil, nil, 500)
  else
    -- JSON encode result, and respond as JSON type
    local result = core.json.encode({ timestamp = res })
    page.response(result, nil, page.JSON)
  end
end

--=============================================================================
--== Handle incoming request
--=============================================================================

local action

--== Incoming values are in the `page.form` object
if page.form then
  -- The `action` is a value we pass from the AJAX call
  action = page.form.action
end

--== Drive application logic based on `action`
if action == 'get-timestamp' then

  -- Call our response method created above
  -- and pass the `page.form` object
  getTimestamp(page.form)

elseif action == 'some-other-action' then
  --== You can add additional logic using `elseif`
else
  -- No `action` or unknown
  page.response("Bad Action", nil, nil, 500)
end

```

!!! info "Server-side Modules"
    For more advanced use cases you can access your custom server-side API modules in the response methods using __[page.callApi](/server/modules/pages/api/#callapi)__.

## Examples

### XMLHttpRequest

```html
<!DOCTYPE html>
<html>
<body>
  <h1>The XMLHttpRequest Object</h1>

  <div id="demo">

    <!-- Post to `pageapi`, no .lua extension -->
    <button type="button"
    onclick="loadDoc('pageapi', myFunction)">Get MySQL Timestamp
    </button>

  </div>

  <script>
    function loadDoc(url, cFunction) {
      //Get UNIX time
      var time = Math.floor(Date.now() / 1000);

      var xhttp;
      
      xhttp=new XMLHttpRequest();
      
      xhttp.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
          cFunction(this);
        }
      };

      xhttp.open("POST", url, true);

      //Set `action` and `time` parameters here
      xhttp.send("action=get-timestamp&time="+time);
    }

    function myFunction(xhttp) {
      document.getElementById("demo").innerHTML = 
      JSON.parse(xhttp.responseText).timestamp;
    }
  </script>
</body>
</html>
```

### Qwest Library

I personally prefer using the __[qwest](https://github.com/pyrsmk/qwest)__ library, as it uses JSON natively, making it easier to work with.

```html
<!DOCTYPE html>
<html>
<head>
<script src="https://cdnjs.cloudflare.com/ajax/libs/qwest/4.5.0/qwest.min.js"></script>
</head>
  <body>

  <h1>The Qwest Library</h1>

  <div id="demo">
    <button type="button" onclick="loadDoc()">Get MySQL Timestamp</button>
  </div>

  <script>
    function loadDoc() {
      //Post to `pageapi`, no .lua extension
      qwest.post('pageapi', {
        //Set `action` and `time` values here
        action: 'get-timestamp',
        time: Math.floor(Date.now() / 1000)
     })
     .then(function(xhr, response) {
        document.getElementById("demo").innerHTML = response.timestamp;
     })
     .catch(function(e, xhr, response) {
        // Process the error
     });
    }
  </script>

  </body>
</html>
```

## Example Files

You can download these example files by __[clicking here](https://s3.amazonaws.com/coronium-core/examples/ajax-pages.zip)__.