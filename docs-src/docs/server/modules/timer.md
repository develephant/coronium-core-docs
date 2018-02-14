<div style="color:Tomato"><i class="fas fa-exclamation-circle"></i> Be sure to read the <a href="/server/guide/lifecycle/">Lifecycle/Context</a> section to understand how timers work.</div>

Timers take up a client connection from your total pool of connections. They also take up memory for as long as they are active. Be careful when using timers, and make sure you have control over it or it could run forever using up resources.

## timer

Creates a timer that will call a function on expiration, or in a recurring fashion.

```lua
core.timer(seconds, callback[, recurring][, extra_args])
```

__Parameters__

|Name|Description|Type|Required|
|----|-----------|----|--------|
|seconds|The amount of seconds before the callback is triggered.|_Number_|__Y__|
|callback|The callback function to call on timer expiry.|_Function_|__Y__|
|recurring|Whether this timer loops. Required if passing arguments.|_Boolean_|__N__|
|extra_args|Any extra arguments to pass to the timer callback.|_Various_|__N__|

__Returns__

A timer __object__, or __nil__, and an error.

__Examples__

Timers run in their own context outside of the client request. For example, the timer callback shown below will ___not___ actually send anything back to the client. Instead the timer is started in its own context and the method immediately returns `true`.

<div style="color:Tomato"><i class="fas fa-exclamation-circle"></i> Be aware that anytime you call the <code>doSomething</code> method you will create a new timer.</div>

```lua
--This example will not work!
local api = core.api()

function api.doSomething()

  local function timer_cb()
    --this won't return data to the client
    return {some=data}
  end

  local t, err = core.timer(5, timer_cb)

  --returns immediately
  return true
end

return api
```

You can however use just about anything else included in the __core__ server-side api:

```lua
local api = core.api()

function api.doSomething()

  local function timer_cb()
    core.log("I'm 5 seconds late to the party!")
  end

  local t, err = core.timer(5, timer_cb)

  --returns immediately
  return true
end

return api
```

You can also pass in parameters that the callback can use on expiry.

```lua
local api = core.api()

function api.doSomething(input)

  local function timer_cb(name)
    core.log(name.." was here 5 seconds ago!")
  end

  local t, err = core.timer(5, timer_cb, false, input.name)

  --returns immediately
  return true
end

return api
```

You can set up recurring timers, but you should make sure you have a way to stop them at a specific point or they run forever. 

To stop a recurring timer use the `timer_obj:cancel()` method (see example below). 

If you need a timer that runs indefinitely it's better to use __[Jobs](/server/modules/jobs/guide/)__.

```lua
local api = core.api()

function api.doSomething()

  --this example uses upvalues
  --which is legal for the timer
  local t, err
  local cnt = 1
  local max_runs = 5

  local function timer_cb()
    core.log("This is callback #"..cnt)

    cnt = cnt + 1
    if cnt > max_runs then
      t:cancel() --cancel the timer
    end
  end

  --repeats every 5 seconds until canceled
  t, err = core.timer(5, timer_cb, true)

  --returns immediately
  return true
end

return api
```