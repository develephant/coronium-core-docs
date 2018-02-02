If you have pages that need to access data in an asynchronous manner via AJAX, you will need to set up a specific structure so that your pages can make calls to internal APIs.

## Server API Module

You will need to create a server-side API that you will call to handle any processing done by the core modules. You can name this directory whatever you'd like, or even create the API project using the webmin.

As a best practice, you should use an underscore in the project name to note that it's being called from a pages module.

```text
projects/
  _webapi/
    main.lua
```

In the `main.lua` we can create a simple method for testing:

```lua
--== _webapi/main.lua
local api = core.api()

function api.whatsMyName(input)
  return {name = input.name}
end

return api
```

## Pages API Module

You will also need to create a "communication" module that you will place in the same directory where your display page(s) reside. This module will talk to your project API (in this case `_webapi`)

```text
pages/
  my_page.lua
  pageapi.lua
```

In the `pageapi.lua` we set up our "cross-calls" by using the __[page.callApi](/server/modules/pages/api/#callapi)__ method. The passed in values from an AJAX call are in the `page.form` object when using the POST method.

!!! note "POST Method"
    For ease of use, you should always make your AJAX calls using the POST method.

```lua
local page = core.pages.new()

--== Create a method for each `action`
local function whatsMyName(input)
  local res = page.callApi("_webapi", "whatsMyName", {name=input.name})

  if not res then
    page.response("Error", nil, nil, 500)
  else
    -- JSON encode response, and respond as JSON type
    page.response(core.json.encode(res), nil, page.JSON)
  end
end

--== Check incoming request ==--

local action

--== Incoming values are in the `page.form` object
if page.form then
  -- The `action` is a value we pass from the AJAX call
  action = page.form.action
end

--== Drive application logic based on `action`
if action == 'whats-my-name' then
  -- Call our action method created above
  -- and pass the `page.form` object
  whatsMyName(page.form)
else
  -- No `action` or unknown
  page.response("Bad Action", nil, nil, 500)
end
```

## Raw AJAX Example

```html
<!DOCTYPE html>
<html>
<body>
  <h1>The XMLHttpRequest Object</h1>

  <div id="demo">

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

## Qwest Lib Example

I personally prefer using the __[qwest](https://github.com/pyrsmk/qwest)__ library, as it uses JSON natively, which happens to be the data type Coronium Core transfers.

```html
<!DOCTYPE html>
<html>
<head>
<script src="https://cdnjs.cloudflare.com/ajax/libs/qwest/4.5.0/qwest.min.js"></script>
</head>
  <body>

  <h1>Using Qwest</h1>

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