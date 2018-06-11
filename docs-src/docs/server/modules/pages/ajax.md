The following explains how to access data in an asynchronous manner via AJAX using the Pages module.

## Page Response Module

You will need to create a "response" module that will placed in the same directory where your display page(s) reside. This module can talk to Coronium Core modules, and then you'll send back the responses.

__Directory Structure__

```text
pages/
  ajax.html
  ajax.lua
  qwest.html
```

In the `ajax.lua` we set up methods to respond to the AJAX page requests. The passed in values from an AJAX call are in the `page.form` object when using the POST method.

!!! note "POST Method"
    For ease of use, you should always make your AJAX calls using the POST method.

__ajax.lua__

```lua
local page = core.pages.new()

local function processPost()

  local action = page.form.action

  if action == "get-timestamp" then
    local res, err = core.mysql.timestamp(page.form.time)
    page.renderJson({timestamp = res})
  else
    page.renderJson({error = "Action not found"})
  end

end

--# incoming
if page.isPost then
  processPost()
else
  page.status(501)
end
```

!!! info "Server-side Modules"
    For more advanced use cases you can access your custom server-side API modules in the response methods using __[page.callApi](/server/modules/pages/api/#callapi)__.

## Examples

### XMLHttpRequest

__ajax.html__

```html
<!DOCTYPE html>
<html>
  <body>
    <h1>The XMLHttpRequest Object</h1>

    <div id="demo">

      <!-- Post to `ajax`, no .lua extension -->
      <button type="button"
      onclick="loadDoc('/ajax', myFunction)">Get MySQL Timestamp
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
        //Set Content Type
        xhttp.setRequestHeader("Content-Type", "application/json")
        //Mark as AJAX request
        xhttp.setRequestHeader("X-Requested-With", "XMLHttpRequest")

        //Set `action` and `time` parameters here
        var data = {
          action: 'get-timestamp',
          time: time
        }
        xhttp.send(JSON.stringify(data));
      }

      function myFunction(xhttp) {
        console.log(xhttp);
        document.getElementById("demo").innerHTML = 
        JSON.parse(xhttp.responseText).timestamp;
      }
    </script>
  </body>
</html>
```

### Qwest Library

I personally prefer using the __[qwest](https://github.com/pyrsmk/qwest)__ library, as it uses JSON natively, making it easier to work with.

__qwest.html__

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
      //Post to `ajax`, no .lua extension
      qwest.post('/ajax', {
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
